# Openlayer3-Cluster
A demo of Cluster by Openlayers3.


````<html lang="en">
<head>
    <link rel="stylesheet" href="https://openlayers.org/en/v4.2.0/css/ol.css" type="text/css">
    <style>
        html,body{
            width: 100%;
            height: 100%;
            margin:0px;
        }
        .map{
            width:100%;
            height:100%;
            margin:0px;
        }
    </style>
    <script src="https://openlayers.org/en/v4.2.0/build/ol.js" type="text/javascript"></script>
    <title>我在这里</title>
</head>
<body>
    <div id="map" class="map"></div>
    <script type="text/javascript">

        var raster = new ol.layer.Tile({
            source: new ol.source.OSM()
        });

        var map = new ol.Map({
            layers: [raster],
            target: 'map',
            view: new ol.View({
                center: [118.1926917085, 24.4954958130],
                zoom: 10
            })
        });
    </script>
</body>
</html>

````