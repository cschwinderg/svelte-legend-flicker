<script lang="ts">
	import { onMount } from 'svelte';

	let view: __esri.MapView | null = null;
	onMount(() => {
		let layerListWidget: __esri.Expand | null = null;

		async function loadMap() {
			const MapView = (await import('@arcgis/core/views/MapView')).default;

			view = new MapView({
				container: 'viewDiv',
				map: {
					basemap: 'streets-vector'
				},
				zoom: 6,
				center: [-80.5629, 35.2696] // longitude, latitude
			});

			const FeatureLayer = (await import('@arcgis/core/layers/FeatureLayer')).default;
			const featureLayer = new FeatureLayer({
				portalItem: {
					id: 'd957997ccee7408287a963600a77f61f'
				}
			});
			view.map.add(featureLayer);

			const GroupLayer = (await import('@arcgis/core/layers/GroupLayer')).default;
			const featureLayer2 = new FeatureLayer({
				portalItem: {
					id: 'a98fd08751a5480c898b7cebe38807f4'
				}
			});
			const featureLayer3 = new FeatureLayer({
				portalItem: {
					id: 'ad7046a2b3854c25aea8945b3baac0a7'
				}
			});
			const featureLayer4 = new FeatureLayer({
				url: 'https://services.arcgis.com/P3ePLMYs2RVChkJx/ArcGIS/rest/services/USA_Counties/FeatureServer/0'
			});
			const groupLayer = new GroupLayer({
				title: 'Group Layer Test',
				layers: [featureLayer2, featureLayer3, featureLayer4]
			});
			view.map.add(groupLayer);

			const Expand = (await import('@arcgis/core/widgets/Expand')).default;

			// layerlist/legend widget
			// const LayerList = (await import('@arcgis/core/widgets/LayerList')).default;
			const Legend = (await import('@arcgis/core/widgets/Legend')).default;
			// const reactiveUtils = await import('@arcgis/core/core/reactiveUtils');
			// const layerList = new LayerList({
			// 	container: document.createElement('div'),
			// 	view,
			// 	listItemCreatedFunction: (event) => {
			// 		const item = event.item;

			// 		if (item.layer.type === 'group') {
			// 			item.open = true;
			// 		} else {
			// 			item.panel = {
			// 				content: 'legend',
			// 				open: true
			// 			};
			// 		}
			// 	}
			// });
			// layerListWidget = new Expand({ content: layerList, expandIcon: 'layers', view });
			// view.ui.add(layerListWidget, { position: 'top-left', index: 2 });

			// layerlist/legend widget
			const legend = new Legend({
				container: document.createElement('div'),
				view
			});
			const legendWidget = new Expand({
				content: legend,
				expanded: true,
				expandTooltip: 'Open Legend',
				collapseTooltip: 'Close Legend',
				expandIcon: 'legend',
				group: 'top-left',
				view
			});
			view.ui.add(legendWidget, { position: 'top-left', index: 3 });
		}
		loadMap();

		return function cleanup() {
			if (!view) return;
			if (layerListWidget) view.ui.remove(layerListWidget);
		};
	});
</script>

<svelte:head>
	<title>Map</title>
	<meta name="description" content="This is a map." />
</svelte:head>

<main>
	<!-- ADD this line: -->
	<button
		on:click={async () => {
			const FeatureLayer = (await import('@arcgis/core/layers/FeatureLayer')).default;
			const featureLayer = new FeatureLayer({
				portalItem: {
					id: 'd957997ccee7408287a963600a77f61f'
				}
			});
			if (view) view.map.add(featureLayer);
		}}>Add Layer</button
	>
	<div class="view" id="viewDiv" />
</main>

<style>
	@import '@arcgis/core/assets/esri/themes/light/main.css';

	.view {
		height: 400px;
		width: 800px;
	}
</style>
