<template>
    <div ref="container" :class="containerClass" @click="onClick">
        <div class="p-hidden-accessible">
            <input ref="focusInput" type="text" role="listbox" readonly :disabled="disabled" @focus="onFocus" @blur="onBlur" @keydown="onKeyDown" :tabindex="tabindex"/>
        </div>
        <div class="p-multiselect-label-container">
            <label :class="labelClass">
                <slot name="value" :value="value">
                    {{label}}
                </slot>
            </label>
        </div>
        <div class="p-multiselect-trigger">
            <span class="p-multiselect-trigger-icon pi pi-chevron-down p-c"></span>
        </div>
        <transition name="p-input-overlay" @enter="onOverlayEnter" @leave="onOverlayLeave">
            <div ref="overlay" class="p-multiselect-panel" v-if="overlayVisible">
                <div class="p-multiselect-header">
                    <div class="p-checkbox p-component" @click="onToggleAll">
                        <div class="p-hidden-accessible">
                            <input type="checkbox" readonly @focus="onHeaderCheckboxFocus" @blur="onHeaderCheckboxBlur">
                        </div>
                        <div :class="['p-checkbox-box p-component', {'p-highlight': allSelected, 'p-focus': headerCheckboxFocused}]">
                            <span :class="['p-checkbox-icon p-c', {'pi pi-check': allSelected}]"></span>
                        </div>
                    </div>
                    <div class="p-multiselect-filter-container">
                        <input type="text" v-model="filterValue" class="p-multiselect-filter p-component p-inputtext p-component" :placeholder="filterPlaceholder">
                        <span class="p-multiselect-filter-icon pi pi-search"></span>
                    </div>
                    <button class="p-multiselect-close p-link" @click="onCloseClick">
                        <span class="p-multiselect-close-icon pi pi-times" />
                    </button>
                </div>
                <div ref="itemsWrapper" class="p-multiselect-items-wrapper" :style="{'max-height': scrollHeight}">
                    <ul class="p-multiselect-items p-multiselect-list p-component">
                        <li v-for="(option, i) of visibleOptions" :class="['p-multiselect-item', {'p-highlight': isSelected(option), 'p-disabled': isOptionDisabled(option)}]"
                            :aria-label="getOptionLabel(option)" :key="getOptionLabel(option)" @click="onOptionSelect($event, option)" @keydown="onOptionKeyDown($event, option)" :tabindex="tabindex||'0'">
                            <div class="p-checkbox p-component">
                                <div :class="['p-checkbox-box p-component', {'p-highlight': isSelected(option)}]">
                                    <span :class="['p-checkbox-icon p-c', {'pi pi-check': isSelected(option)}]"></span>
                                </div>
                            </div>
                            <slot name="option" :option="option" :index="i">
                                <span>{{getOptionLabel(option)}}</span>
                            </slot>
                        </li>
                    </ul>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
import ObjectUtils from '../utils/ObjectUtils';
import DomHandler from '../utils/DomHandler';

export default {
    props: {
        value: null,
        options: Array,
		optionLabel: null,
		scrollHeight: {
			type: String,
			default: '200px'
		},
		placeholder: String,
		disabled: Boolean,
		filter: Boolean,
		tabindex: String,
        dataKey: null,
        optionValue: null,
        optionDisabled: null,
        filterPlaceholder: String
    },
    data() {
        return {
            focused: false,
            headerCheckboxFocused: false,
            filterValue: null,
            overlayVisible: false
        };
    },
    outsideClickListener: null,
    beforeDestroy() {
        this.unbindOutsideClickListener();
    },
    updated() {
        if (this.overlayVisible && this.filterValue) {
            this.alignOverlay();
        }
    },
    methods: {
        getOptionLabel(option) {
            return ObjectUtils.resolveFieldData(option, this.optionLabel);
        },
        getOptionValue(option) {
            return this.optionValue ? ObjectUtils.resolveFieldData(option, this.optionValue) : option;
        },
        isOptionDisabled(option) {
            return this.optionDisabled ? ObjectUtils.resolveFieldData(option, this.optionDisabled) : false;
        },
        isSelected(option) {
            let selected = false;
            let optionValue = this.getOptionValue(option);

            if (this.value) {
                for (let val of this.value) {
                    if (ObjectUtils.equals(val, optionValue, this.dataKey)) {
                        selected = true;
                        break;
                    }
                }
            }

            return selected;
        },
        onFocus() {
            this.focused = true;
        },
        onBlur() {
            this.focused = false;
        },
        onHeaderCheckboxFocus() {
            this.headerCheckboxFocused = true;
        },
        onHeaderCheckboxBlur() {
            this.headerCheckboxFocused = false;
        },
        onClick() {
            if (!this.$refs.overlay || !this.$refs.overlay.contains(event.target)) {
                this.overlayVisible = !this.overlayVisible;
                this.$refs.focusInput.focus();
            }
        },
        onCloseClick() {
            this.overlayVisible = false;
        },
        onKeyDown(event) {
            switch(event.which) {
                //down
                case 40:
                    if (this.visibleOptions && !this.overlayVisible && event.altKey) {
                        this.overlayVisible = true;
                    } 
                break;

                //space
                case 32:
                    if (!this.overlayVisible) {
                        this.overlayVisible = true;
                        event.preventDefault();
                    }
                break;

                //enter and escape
                case 13:
                case 27:
                    if (this.overlayVisible) {
                        this.overlayVisible = false;
                        event.preventDefault();
                    }
                break;

                //tab
                case 9:
                    this.overlayVisible = false;
                break;

                default:
                break;
            }
        },
        onOptionSelect(event, option) {
            if (this.disabled || this.isOptionDisabled(option)) {
                return;
            }

            let selected = this.isSelected(option);
            let value = null;

            if (selected)
                value = this.value.filter(val => !ObjectUtils.equals(val, this.getOptionValue(option), this.dataKey));
            else
                value = [...this.value || [], this.getOptionValue(option)];

            this.$emit('input', value);
            this.$emit('change', {originalEvent: event, value: value});
        },
        onOptionKeyDown(event, option) {
            let listItem = event.target;
        
            switch(event.which) {
                //down
                case 40:
                    var nextItem = this.findNextItem(listItem);
                    if (nextItem) {
                        nextItem.focus();
                    }
                    
                    event.preventDefault();
                break;
                
                //up
                case 38:
                    var prevItem = this.findPrevItem(listItem);
                    if (prevItem) {
                        prevItem.focus();
                    }
                    
                    event.preventDefault();
                break;
                
                //enter
                case 13:
                    this.onOptionSelect(event, option);
                    event.preventDefault();
                break;

                default:
                break;
            }
        },
        findNextItem(item) {
            let nextItem = item.nextElementSibling;

            if (nextItem)
                return DomHandler.hasClass(nextItem, 'p-disabled') ? this.findNextItem(nextItem) : nextItem;
            else
                return null;
        },
        findPrevItem(item) {
            let prevItem = item.previousElementSibling;
            
            if (prevItem)
                return DomHandler.hasClass(prevItem, 'p-disabled') ? this.findPrevItem(prevItem) : prevItem;
            else
                return null;
        },
        onOverlayEnter() {
            this.alignOverlay();
            this.bindOutsideClickListener();
        },
        onOverlayLeave() {
            this.unbindOutsideClickListener();
        },
        alignOverlay() {
            DomHandler.relativePosition(this.$refs.overlay, this.$refs.container);
        },
        bindOutsideClickListener() {
            if (!this.outsideClickListener) {
                this.outsideClickListener = (event) => {
                    if (this.overlayVisible && this.isOutsideClicked(event)) {
                        this.overlayVisible = false;
                    }
                };
                document.addEventListener('click', this.outsideClickListener);
            }
        },
        unbindOutsideClickListener() {
            if (this.outsideClickListener) {
                document.removeEventListener('click', this.outsideClickListener);
                this.outsideClickListener = null;
            }
        },
        isOutsideClicked(event) {
            return !(this.$refs.container.isSameNode(event.target) || this.$refs.container.contains(event.target) || (this.$refs.overlay && this.$refs.overlay.contains(event.target)));
        },
        getLabelByValue(val) {
            let label = null;

            if (this.options) {
                for (let option of this.options) {
                    let optionValue = this.getOptionValue(option);

                    if(ObjectUtils.equals(optionValue, val, this.dataKey)) {
                        label = this.getOptionLabel(option);
                        break;
                    }
                }
            }

            return label;
        },
        onToggleAll(event) {
            const value = this.allSelected ? [] : this.visibleOptions;

            this.$emit('input', value);
            this.$emit('change', {originalEvent: event, value: value});
        }
    },
    computed: {
        visibleOptions() {
            if (this.filterValue && this.filterValue.trim().length > 0)
                return this.options.filter(option => this.getOptionLabel(option).toLowerCase().indexOf(this.filterValue.toLowerCase()) > -1);
            else
                return this.options;
        },
        containerClass() {
            return [
                'p-multiselect p-component p-unselectable-text',
                {
                    'p-disabled': this.disabled,
                    'p-focus': this.focused
                }
            ];
        },
        labelClass() {
            return [
                'p-multiselect-label', 
                {
                    'p-placeholder': this.label == null && this.placeholder, 
                    'p-multiselect-label-empty': !this.placeholder
                }
            ];
        },
        label() {
            let label;

            if (this.value && this.value.length) {
                label = '';
                for(let i = 0; i < this.value.length; i++) {
                    if(i !== 0) {
                        label += ',';
                    }

                    label += this.getLabelByValue(this.value[i]);
                }
            }
            else {
                label = this.placeholder || 'p-multiselect';
            }

            return label;
        },
        allSelected() {
            if (this.filterValue && this.filterValue.trim().length > 0) {
                let allSelected = true;
                for (let option of this.visibleOptions) {
                    if (!this.isSelected(option)) {
                        allSelected = false;
                        break;
                    }
                }
                return allSelected;
            }
            else {
                return this.value && this.options && (this.value.length > 0 && this.value.length === this.options.length);
            }                
        }
    }
}
</script>