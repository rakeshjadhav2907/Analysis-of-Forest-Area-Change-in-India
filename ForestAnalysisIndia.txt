var countryBound = ee.FeatureCollection("FAO/GAUL_SIMPLIFIED_500m/2015/level0");
Map.addLayer(countryBound);

var country = countryBound
    .filter(ee.Filter.inList('ADM0_NAME', ['India', 'Jammu and Kashmir', 'Aksai Chin', 'Arunachal Pradesh']));
    // 
Map.addLayer(country, {color : 'lavender'}, 'Selected');

Map.centerObject(country);

var gfc2019 = ee.Image("UMD/hansen/global_forest_change_2019_v1_7").clip(country);
/*
Map.addLayer(gfc2019, {
  bands: ['treecover2000'],
  palette: ['000000', '00FF00'],
   max: 100
 }, 'forest cover percent');*/

// Map.addLayer(gfc2019, {
//   bands: ['loss', 'treecover2000', 'gain'],
//   max: [1, 255, 1]
// }, 'forest cover, loss, gain');

Map.addLayer(gfc2019.mask(gfc2019), {
  bands: ['treecover2000'],
  palette: ['000000', '00FF00'],
  max: 100
}, 'forest cover masked');

var treeCover = gfc2019.select(['treecover2000']);
var lossImage = gfc2019.select(['loss']);
var gainImage = gfc2019.select(['gain']);

// Add the tree cover layer in green.
Map.addLayer(treeCover.updateMask(treeCover),
    {palette: ['000000', '00FF00'], max: 100}, 'Forest Cover');

// Add the loss layer in red.
Map.addLayer(lossImage.updateMask(lossImage),
            {palette: ['FF0000']}, 'Loss');

// Add the gain layer in blue.
Map.addLayer(gainImage.updateMask(gainImage),
            {palette: ['0000FF']}, 'Gain');
