## Html5定位+百度地图示例  
- HTML5 Geolocation API 实现地理位置的定位  
- 通过定位拿到经纬度，再使用百度地图或谷歌地图API 实现地图展示和城市信息获取  
- 代码中的KEY需要自行申请  
 
```html
<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="initial-scale=1.0, user-scalable=no" />
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <title>Html5定位+百度地图示例</title>
    <style type="text/css">
    html {
        height: 100%
    }
    body {
        height: 100%;
        margin: 0px;
        padding: 0px
    }
    #container {
        height: 100%
    }
    </style>
    <script type="text/javascript" src="./jquery.js"></script>
    <script type="text/javascript" src="http://api.map.baidu.com/api?v=2.0&ak=KEY"></script>
    <!-- <script type="text/javascript" src="map.js"></script> -->
</head>

<body>
    <div id="container"></div>
    <script type="text/javascript">
    var Maps = {
        positionOptions: {
            // 指示浏览器获取高精度的位置，默认为false
            enableHighAccuracy: true,
            // 指定获取地理位置的超时时间，默认不限时，单位为毫秒
            timeout: 5000,
            // 最长有效期，在重复获取地理位置时，此参数指定多久再次获取位置。
            maximumAge: 3000
        },
        getUserLocation: function() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(this.locationSuccess, this.locationError, this.positionOptions);
            } else {
                alert("Your browser does not support Geolocation!");
            }
        },

        locationSuccess: function(position) {
            var coords = position.coords;
            // baidu
            Maps.showBaiduMap(coords);
            Maps.getAddressByPosition(coords);

        },
        locationError: function(error) {
            switch (error.code) {
                case error.TIMEOUT:
                    showError("A timeout occured! Please try again!");
                    break;
                case error.POSITION_UNAVAILABLE:
                    showError('We can\'t detect your location. Sorry!');
                    break;
                case error.PERMISSION_DENIED:
                    showError('Please allow geolocation access for this to work.');
                    break;
                case error.UNKNOWN_ERROR:
                    showError('An unknown error occured!');
                    break;
            }
        },
        getAddressByPosition: function(position) {
            var apiUrl = 'http://api.map.baidu.com/geocoder/v2/?ak=KEY&location=' + position.latitude + ',' + position.longitude + '&output=json';
            $.ajax({
                url: apiUrl,
                type: 'GET',
                cache: false,
                dataType: 'jsonp',
                jsonpCallback: 'jsonpCallback',
                success: function(data, status) {
                    var cName = data.result.addressComponent.city;
                    alert('您所在城市为:' + cName)
                    cName = cName.substr(0, 2);
                },
                error: function(info) {
                    alert('error')
                }
            });
        },
        showBaiduMap: function(position) {
            // alert(position.longitude + '/'+ position.latitude)
            var map = new BMap.Map("container"); // 创建地图实例  
            var point = new BMap.Point(position.longitude, position.latitude); // 创建点坐标  
            map.centerAndZoom(point, 15); // 初始化地图，设置中心点坐标和地图级别  
        },
        showGoogleMap: function() {
            // Google
            var latlng = new google.maps.LatLng(
                // 地理纬度
                coords.latitude,
                // 地理经度
                coords.longitude
            );
            var myOptions = {
                // 地图放大倍数  
                zoom: 12,
                // 地图中心设为指定坐标点  
                center: latlng,
                // 地图类型  
                mapTypeId: google.maps.MapTypeId.ROADMAP
            };
            // 创建地图并输出到页面  
            var myMap = new google.maps.Map(
                document.getElementById("map"), myOptions
            );
            // 创建标记  
            var marker = new google.maps.Marker({
                // 标注指定的经纬度坐标点  
                position: latlng,
                // 指定用于标注的地图  
                map: myMap
            });
            //创建标注窗口  
            var infowindow = new google.maps.InfoWindow({
                content: "您在这里<br/>纬度：" +
                    coords.latitude +
                    "<br/>经度：" + coords.longitude
            });
            //打开标注窗口  
            infowindow.open(myMap, marker);
        }
    }

    function showError(msg) {
        alert(msg)
    }
    $(function() {
        Maps.getUserLocation();
    })
    </script>
</body>
</html>
```
