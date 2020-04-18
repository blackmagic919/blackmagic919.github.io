title: First post
date: 2020-04-10
category: test

# My First Post

Avinci Club Sucks


<div id="container2" style="height: 520px; width: 100%; margin: 0 auto; text-align:center; line-height: 520px">
  Downloading Pi Chart...
</div>


<div id="container" style="height: 520px; width: 100%; margin: 0 auto; text-align:center; line-height: 520px">
  Downloading map...
</div>

<script src="https://code.highcharts.com/maps/highmaps.js"></script>
<script src="https://code.highcharts.com/maps/modules/data.js"></script>
<script src="https://code.highcharts.com/maps/modules/data.js"></script>
<script src="https://code.highcharts.com/maps/modules/exporting.js"></script>
<script src="https://code.highcharts.com/maps/modules/offline-exporting.js"></script>
<script src="https://code.highcharts.com/mapdata/countries/us/us-nj-all.js"></script>


<script>

Highcharts.getJSON(
  'https://services1.arcgis.com/0MSEUqKaxRlEPj5g/arcgis/rest/services/COVID19_Counties_View/FeatureServer/0/query?where=state_fips%3D34&objectIds=&time=&geometry=&geometryType=esriGeometryEnvelope&inSR=&spatialRel=esriSpatialRelIntersects&resultType=none&distance=0.0&units=esriSRUnit_Meter&returnGeodetic=false&outFields=*&returnGeometry=false&returnCentroid=false&featureEncoding=esriDefault&multipatchOption=xyFootprint&maxAllowableOffset=&geometryPrecision=&outSR=&datumTransformation=&applyVCSProjection=false&returnIdsOnly=false&returnUniqueIdsOnly=false&returnCountOnly=false&returnExtentOnly=false&returnQueryGeometry=false&returnDistinctValues=false&cacheHint=false&orderByFields=&groupByFieldsForStatistics=&outStatistics=&having=&resultOffset=&resultRecordCount=&returnZ=false&returnM=false&returnExceededLimitFeatures=true&quantizationParameters=&sqlFormat=none&f=pjson&token=',
  

  
  function (data) {
    
    data = data.features;
    data.forEach(function(e){
      e.value = (parseInt(e.attributes.confirmed_per_100k)/10000).toString();
      e.code = e.attributes.fips;
      e.y = e.attributes.confirmed;
      e.name = e.attributes.county;
    })

    /**
     * Data parsed from http://www.bls.gov/lau/#tables
     *
     * 1. Go to http://www.bls.gov/lau/laucntycur14.txt (or similar, updated
     *  datasets)
     * 2. In the Chrome Developer tools console, run this code:
     *  copy(JSON.stringify(document.body.innerHTML.split('\n').filter(function (s) { return s.indexOf('<PUT DATE HERE IN FORMAT e.g. Feb-14>') !== -1; }).map(function (row) { row = row.split('|'); return { code: 'us-' + row[3].trim().slice(-2).toLowerCase() + '-' + row[2].trim(), name: row[3].trim(), value: parseFloat(row[8]) }; })))
     * 3. The data is now on your clipboard, paste it below
     * 4. Verify that the length of the data is reasonable, about 3300
     *  counties.
     */
    Highcharts.chart('container2', {
        chart: {
            plotBackgroundColor: null,
            plotBorderWidth: null,
            plotShadow: false,
            type: 'pie'
        },
        title: {
            text: 'NJ Coronavirus Confirmed Cases'
        },
        plotOptions: {
            pie: {
                allowPointSelect: true,
                cursor: 'pointer',
                dataLabels: {
                    enabled: true,
                    format: '<b>{point.name}</b>: {point.y}'
                }
            }
        },
        series: [{
            name: 'Confirmed',
            colorByPoint: true,
            data: data
            
        }]
    });
    var countiesMap = Highcharts.geojson(
        Highcharts.maps['countries/us/us-nj-all']
      ),
      // Extract the line paths from the GeoJSON
      lines = Highcharts.geojson(
        Highcharts.maps['countries/us/us-nj-all'], 'mapline'
      ),
      // Filter out the state borders and separator lines, we want these
      // in separate series
      borderLines = Highcharts.grep(lines, function (l) {
        return l.properties['hc-group'] === '__border_lines__';
      }),
      separatorLines = Highcharts.grep(lines, function (l) {
        return l.properties['hc-group'] === '__separator_lines__';
      });

    // Add state acronym for tooltip
    Highcharts.each(countiesMap, function (mapPoint) {
      mapPoint.name = mapPoint.name + ', ' +
        mapPoint.properties['hc-key'].substr(3, 2);
    });

    document.getElementById('container').innerHTML = 'Rendering map...';

    // Create the map
    setTimeout(function () { // Otherwise innerHTML doesn't update
      Highcharts.mapChart('container', {
        chart: {
          borderWidth: 1,
          marginRight: 20 // for the legend
        },

        title: {
          text: 'US Counties Coronavirus Rates'
        },

        legend: {
          layout: 'vertical',
          align: 'right',
          floating: true,
          backgroundColor: ( // theme
            Highcharts.defaultOptions &&
            Highcharts.defaultOptions.legend &&
            Highcharts.defaultOptions.legend.backgroundColor
          ) || 'rgba(255, 255, 255, 0.85)'
        },

        mapNavigation: {
          enabled: true
        },

        colorAxis: {
          min: 0,
          max: 0.06,
          tickInterval: 0.01,
          stops: [[0, '#FFFFFF'], [0.5, '#00E0FF'], [1, '#0051FF']],
          labels: {
            format: '{value}%'
          }
        },

        plotOptions: {
          mapline: {
            showInLegend: false,
            enableMouseTracking: false
          }
        },

        series: [{
          mapData: countiesMap,
          data: data,
          joinBy: ['fips', 'code'],
          name: 'Infected Rate',
          tooltip: {
            valueSuffix: '%'
          },
          borderWidth: 0.5,
          states: {
            hover: {
              color: '#00245F'
            }
          },
          shadow: false
        }, {
          type: 'mapline',
          name: 'State borders',
          data: borderLines,
          color: 'white',
          shadow: false
        }, {
          type: 'mapline',
          name: 'Separator',
          data: separatorLines,
          color: 'gray',
          shadow: false
        }]
      });
    }, 0);
  }
);
</script>