 
 <script>
    var map = null;

function initMap() {
    var myOptions = {
        zoom: 7,
        center: new google.maps.LatLng(39, 35),
        mapTypeControl: true,
        mapTypeControlOptions: {
            style: google.maps.MapTypeControlStyle.DROPDOWN_MENU
        },
        navigationControl: true,
        mapTypeId: google.maps.MapTypeId.ROADMAP
    };
    map = new google.maps.Map(document.getElementById("map"), myOptions);
 	
    var locations = [
        <cfoutput query="getcomp"><!--- Your Locations --->
        { lat: #COORDINATE_1#, lng: #COORDINATE_2# },
        </cfoutput>
    ];
	var marker, i;

	/* for (i = 0; i < locations.length; i++) {
		marker = new google.maps.Marker({
			position: new google.maps.LatLng(locations[i].lat, locations[i].lng),
			map: map,
			icon: {
				url: '/documents/etionline/icon/3.png',
				scaledSize: new google.maps.Size(15, 15),
				origin: new google.maps.Point(0, 0),
				anchor: new google.maps.Point(8, 22)
			}
		});

		var infowindow = new google.maps.InfoWindow();
		google.maps.event.addListener(marker, 'click', (function(marker, i) {
			return function() {
				infowindow.setContent(locationInfoStart[i][0]);
				infowindow.open(map, marker);
			}
		})(marker, i));
	} */

    var point = new google.maps.LatLng(42.2853494511775, 26.246073145905257);
 	var hexagons = []; // Hexagonları saklamak için bir dizi oluşturun
	createDefaultHexagon();
	var hexagonCreationFlag = false;

	google.maps.event.addListener(map, 'zoom_changed', function() {
		var zoomLevel = map.getZoom();
 		if(zoomLevel > 9 && !hexagonCreationFlag){
 			radiuschange();
			 hexagonCreationFlag = true;
		}
		else if (zoomLevel <= 9 && hexagonCreationFlag)  
 		{
			createDefaultHexagon()
			hexagonCreationFlag = false;
		}
	});
	function radiuschange() {
			var radius =<cfoutput>#getset.ZOOMSIZE#</cfoutput>;
			var hexagonHeight = Math.sqrt(3) * radius;
			var newright =  <cfoutput>405000 /radius</cfoutput>;
			var newleft =  <cfoutput>945000/ radius</cfoutput>;
			clearHexagons();
			for (var i = 0; i < newright; i++) {
				for (var j = 0; j < newleft; j++) {
					var offsetX = 3 / 2 * radius * j;
					var offsetY = (i * 2 + (j % 2)) * hexagonHeight * 0.5;
					var center = EOffsetBearing(EOffsetBearing(point, offsetX, 90), offsetY, 180);

					var vertices = getAllHexagonVertices(center, radius);
		
					// Her altıgen için ayrı bir konum listesi oluştur
					var altigenLocations = [];
					for (var k = 0; k < locations.length; k++) {
						if (isLocationInsideHexagon(locations[k], vertices)) {
							altigenLocations.push(locations[k]);
						}
					}

					// Her altıgenin içindeki nokta sayısını hesapla
					var count = altigenLocations.length;
					var fillColor = '#000'; // Varsayılan renk	
					<cfoutput query="getdef">
						if (count < #max_value# && count > #min_value#) {
						fillColor = '###replace(color,'##','')#'; // Kırmızı renk, nokta sayısı arttıkça renk değişebilir
					}
					</cfoutput>
					
					

					var hex = google.maps.Polygon.RegularPoly(center, radius, 6, 0, fillColor, 1, 1, fillColor, 0.5);
					hex.setMap(map);
					hexagons.push(hex); 
				}
			} 
	}
	function createDefaultHexagon() {
		clearHexagons();
		var radius = <cfoutput>#getset.SIZE#</cfoutput>;
		var hexagonHeight = Math.sqrt(3) * radius;
		var right =  <cfoutput>405000 /#getset.SIZE#</cfoutput>;
		var left =  <cfoutput>945000 /#getset.SIZE#</cfoutput>;
		for (var i = 0; i < right; i++) {
			for (var j = 0; j < left; j++) {
				var offsetX = 3 / 2 * radius * j;
				var offsetY = (i * 2 + (j % 2)) * hexagonHeight * 0.5;
				var center = EOffsetBearing(EOffsetBearing(point, offsetX, 90), offsetY, 180);

				var vertices = getAllHexagonVertices(center, radius);
	
				// Her altıgen için ayrı bir konum listesi oluştur
				var altigenLocations = [];
				for (var k = 0; k < locations.length; k++) {
					if (isLocationInsideHexagon(locations[k], vertices)) {
						altigenLocations.push(locations[k]);
					}
				}

				// Her altıgenin içindeki nokta sayısını hesapla
				var count = altigenLocations.length;
				if (count == 0){
					console.log(count)
				}
				else{
					var fillColor = '#000'; // Varsayılan renk	
					<cfoutput query="getdef">
						if (count < #max_value# && count > #min_value#) {
						fillColor = '###replace(color,'##','')#'; // Kırmızı renk, nokta sayısı arttıkça renk değişebilir
					}
					</cfoutput> 
					var hex = google.maps.Polygon.RegularPoly(center, radius, 6, 0, fillColor, 1, 1, fillColor, 0.5);
					hex.setMap(map);
					hexagons.push(hex); 
				}
					
			}
		} 
	}
	function clearHexagons() {
		for (var i = 0; i < hexagons.length; i++) {
			hexagons[i].setMap(null); // Her hexagonu haritadan kaldır
		}
		hexagons = []; // hexagons dizisini temizle
	}
	 
}
	function getAllHexagonVertices(center, radius) {
		var vertices = [];
		for (var i = 0; i < 6; i++) {
			var angleInDegrees = 60 * i + 30;
			var angleInRadians = angleInDegrees * Math.PI / 180;
			var vertexLat = center.lat() + (radius * Math.sin(angleInRadians) / 111111);
			var vertexLng = center.lng() + (radius * Math.cos(angleInRadians) / (111111 * Math.cos(center.lat() * Math.PI / 180)));
			vertices.push({
				lat: vertexLat,
				lng: vertexLng
			});
		}
		return vertices;
	}


	function countLocationsInsideHexagon(locations, hexagons) {
		var counts = []; // Her altıgen için nokta sayısını tutacak dizi
		for (var j = 0; j < hexagons.length; j++) {
			var count = 0; // Her altıgen için yeni bir sayaç
			var hexagon = hexagons[j];

			for (var i = 0; i < locations.length; i++) {
				var location = locations[i];
				if (isLocationInsideHexagon(location, hexagon)) {
					count++;
				}
			}
			counts.push(count); // Her altıgen için bulunan nokta sayısını diziye ekleyin
		}
		return counts;
	} 

	function isLocationInsideHexagon(location, hexagon) {
		var x = location.lat;
		var y = location.lng;
		var isInside = false;

		for (var i = 0; i < hexagon.length; i++) {
			var xi = hexagon[i].lat,
				yi = hexagon[i].lng;
			var j = i === 0 ? hexagon.length - 1 : i - 1;
			var xj = hexagon[j].lat,
				yj = hexagon[j].lng;

			var intersect = ((yi > y) != (yj > y)) && (x < (xj - xi) * (y - yi) / (yj - yi) + xi);
			if (intersect) {
				isInside = !isInside;
			}
		}
		return isInside;
	}

	function EOffsetBearing(point, dist, bearing) {
		var latConv = google.maps.geometry.spherical.computeDistanceBetween(point, new google.maps.LatLng(point.lat() + 0.1, point.lng())) * 10;
		var lngConv = google.maps.geometry.spherical.computeDistanceBetween(point, new google.maps.LatLng(point.lat(), point.lng() + 0.1)) * 10;
		var lat = dist * Math.cos(bearing * Math.PI / 180) / latConv;
		var lng = dist * Math.sin(bearing * Math.PI / 180) / lngConv;
		return new google.maps.LatLng(point.lat() + lat, point.lng() + lng);
	}

	google.maps.event.addDomListener(window, 'load', initMap);

	google.maps.Polygon.Shape = function(point, r1, r2, r3, r4, rotation, vertexCount, strokeColour, strokeWeight, Strokepacity, fillColour, fillOpacity, opts, tilt) {
		var rot = -rotation * Math.PI / 180;
		var points = [];
		var latConv = google.maps.geometry.spherical.computeDistanceBetween(point, new google.maps.LatLng(point.lat() + 0.1, point.lng())) * 10;
		var lngConv = google.maps.geometry.spherical.computeDistanceBetween(point, new google.maps.LatLng(point.lat(), point.lng() + 0.1)) * 10;
		var step = (360 / vertexCount) || 10;

		var flop = -1;
		var I1 = tilt ? 180 / vertexCount : 0;

		for (var i = I1; i <= 360.001 + I1; i += step) {
			var r1a = flop ? r1 : r3;
			var r2a = flop ? r2 : r4;
			flop = -1 - flop;
			var y = r1a * Math.cos(i * Math.PI / 180);
			var x = r2a * Math.sin(i * Math.PI / 180);
			var lng = (x * Math.cos(rot) - y * Math.sin(rot)) / lngConv;
			var lat = (y * Math.cos(rot) + x * Math.sin(rot)) / latConv;
			points.push(new google.maps.LatLng(point.lat() + lat, point.lng() + lng));
		}
		return (new google.maps.Polygon({
			paths: points,
			strokeColor: strokeColour,
			strokeWeight: strokeWeight,
			strokeOpacity: Strokepacity,
			fillColor: fillColour,
			fillOpacity: fillOpacity
		}));
	};

	google.maps.Polygon.RegularPoly = function(point, radius, vertexCount, rotation, strokeColour, strokeWeight, Strokepacity, fillColour, fillOpacity, opts) {
		rotation = rotation || 0;
		var tilt = !(vertexCount & 1);
		return google.maps.Polygon.Shape(point, radius, radius, radius, radius, rotation, vertexCount, strokeColour, strokeWeight, Strokepacity, fillColour, fillOpacity, opts, tilt);
	}; 
</script>  
