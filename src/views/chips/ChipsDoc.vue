<template>
	<div class="content-section documentation">
		<TabView>
			<TabPanel header="Documentation">
				<h3>Import</h3>
<CodeHighlight lang="javascript">
import Chips from 'primevue/chips';
</CodeHighlight>

				<h3>Getting Started</h3>
				<p>Chips requires an array as its model.</p>
<CodeHighlight>
&lt;Chips v-model=&quot;value&quot; /&gt;
</CodeHighlight>

				<h3>Custom Content</h3>
				<p>A chip is customized using a template element where the value is passed as the implicit variable.</p>
<CodeHighlight>
<template v-pre>
&lt;Chips v-model=&quot;value&quot;&gt;
	&lt;template #chip=&quot;slotProps&quot;&gt;
		&lt;div&gt;
			&lt;span&gt;{{slotProps.value}} - (active) &lt;/span&gt;
			&lt;i class=&quot;pi pi-user-plus&quot; style=&quot;font-size: 14px&quot;&gt;&lt;/i&gt;
		&lt;/div&gt;
	&lt;/template&gt;
&lt;/Chips&gt;
</template>
</CodeHighlight>

				<h3>Properties</h3>
				<div class="doc-tablewrapper">
					<table class="doc-table">
						<thead>
						<tr>
							<th>Name</th>
							<th>Type</th>
							<th>Default</th>
							<th>Description</th>
						</tr>
						</thead>
						<tbody>
						<tr>
							<td>value</td>
							<td>array</td>
							<td>null</td>
							<td>Value of the component.</td>
						</tr>
						<tr>
							<td>disabled</td>
							<td>boolean</td>
							<td>false</td>
							<td>When present, it specifies that the element should be disabled.</td>
						</tr>
						<tr>
							<td>max</td>
							<td>number</td>
							<td>null</td>
							<td>Maximum number of entries allowed.</td>
						</tr>
						</tbody>
					</table>
				</div>

				<h3>Events</h3>
				<div class="doc-tablewrapper">
					<table class="doc-table">
						<thead>
						<tr>
							<th>Name</th>
							<th>Parameters</th>
							<th>Description</th>
						</tr>
						</thead>
						<tbody>
						<tr>
							<td>input</td>
							<td>value: New value of the component</td>
							<td>Callback to invoke on input event of input field.</td>
						</tr>
						<tr>
							<td>focus</td>
							<td>event: Browser event</td>
							<td>Callback to invoke when a input is focused.</td>
						</tr>
						<tr>
							<td>blur</td>
							<td>event: Browser event</td>
							<td>Callback to invoke when a input loses focus.</td>
						</tr>
						<tr>
							<td>add</td>
							<td>originalEvent: Browser event <br/>
								value: Added item value</td>
							<td>Callback to invoke when a chip is added.</td>
						</tr>
						<tr>
							<td>remove</td>
							<td>originalEvent: Browser event <br/>
								value: Removed item value</td>
							<td>Callback to invoke when a chip is removed.</td>
						</tr>
						</tbody>
					</table>
				</div>

				<h3>Styling</h3>
				<p>Following is the list of structural style classes, for theming classes visit <router-link to="/theming">theming</router-link> page.</p>
				<div class="doc-tablewrapper">
					<table class="doc-table">
						<thead>
						<tr>
							<th>Name</th>
							<th>Element</th>
						</tr>
						</thead>
						<tbody>
						<tr>
							<td>p-chips</td>
							<td>Container element</td>
						</tr>
						<tr>
							<td>p-chips-token</td>
							<td>Chip element container.</td>
						</tr>
						<tr>
							<td>p-chips-token-icon</td>
							<td>Icon of a chip.</td>
						</tr>
						<tr>
							<td>p-chips-token-label</td>
							<td>label of a chip.</td>
						</tr>
						<tr>
							<td>p-chips-input-token</td>
							<td>Container of input element.</td>
						</tr>
						</tbody>
					</table>
				</div>

				<h3>Dependencies</h3>
				<p>None.</p>
			</TabPanel>

			<TabPanel header="Source">
				<a href="https://github.com/primefaces/primevue/tree/master/src/views/chips" class="btn-viewsource" target="_blank" rel="noopener noreferrer">
					<span>View on GitHub</span>
				</a>
<CodeHighlight>
<template v-pre>
&lt;template&gt;
	&lt;div&gt;
		&lt;div class=&quot;content-section introduction&quot;&gt;
			&lt;div class=&quot;feature-intro&quot;&gt;
				&lt;h1&gt;Chips&lt;/h1&gt;
				&lt;p&gt;Chips is used to enter multiple values on an input field.&lt;/p&gt;
			&lt;/div&gt;
		&lt;/div&gt;

		&lt;div class=&quot;content-section implementation&quot;&gt;
			&lt;h3 class=&quot;first&quot;&gt;Basic&lt;/h3&gt;
			&lt;Chips v-model=&quot;value1&quot; /&gt;

			&lt;h3&gt;Template&lt;/h3&gt;
			&lt;Chips v-model=&quot;value2&quot;&gt;
				&lt;template #chip=&quot;slotProps&quot;&gt;
					&lt;div&gt;
						&lt;span&gt;{{slotProps.value}} - (active) &lt;/span&gt;
						&lt;i class=&quot;pi pi-user-plus&quot; style=&quot;font-size: 14px&quot;&gt;&lt;/i&gt;
					&lt;/div&gt;
				&lt;/template&gt;
			&lt;/Chips&gt;
		&lt;/div&gt;
	&lt;/div&gt;
&lt;/template&gt;
</template>
</CodeHighlight>

<CodeHighlight lang="javascript">
export default {
	data() {
		return {
			value1: null,
			value2: null
		}
	}
}
</CodeHighlight>

<CodeHighlight lang="css">
.p-chips .p-inputtext {
	width: 100%;
}
</CodeHighlight>
			</TabPanel>
		</TabView>
	</div>
</template>