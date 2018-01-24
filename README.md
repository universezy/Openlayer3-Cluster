# Openlayer3学习心得之Cluster

## 前言

最近在学习开源地图引擎——Openlayers3，将自己的一些学习心得分享出来，第一次写文章，望支持。

首先放几个链接，都是我学习过程中查阅资料用到的，新人接触这个值得好好学习一下：

[简书——Openlayer3入门指南](https://www.jianshu.com/p/6785e755fa0d)

[Openlayer3 基础教程](http://anzhihun.coding.me/ol3-primer/index.html)

[开始你的第一个Openlayer3项目](http://openlayers.org/en/latest/doc/quickstart.html)（导航栏的示例、API文档都很重要，新手一定要好好看）

如何呈现一副地图就不在这里多述了，上面教程里都有，下面开始讲今天的主题——集群Cluster：

---
### 1. 对于静态数据的Cluster，流程基本是：

- 把每个点按照坐标生成对应的feature：

````
feature = new ol.Feature(new ol.geom.Point(coordinate));
````

**coordinate是点的坐标，格式为 [lon, lat] ，这里要注意一下，很多人会发现画出来的点位置跟预期不符，是因为不同的地图用的坐标格式不一样，这就需要转换坐标了，请前往Openlayer3 基础教程查看坐标转换。**

- feature记得添加到features数组里面，然后把features数组放进Vector里：

````
var source = new ol.source.Vector({
        features: features
      });
````

- 接下来就是重点了，设置集群：

````
 var clusterSource = new ol.source.Cluster({
        distance:clusterDistance,
        source: source
      });
````

- clusterDistance是设置的聚合距离，在这个范围内的点就会聚合在一起。接下来设置聚合点集群的图层：

````
clusters = new ol.layer.Vector({
    			source: clusterSource,
    			style:function(feature) {
    				return setClusterStyle(feature);
    			}
    		});
````

- 这里style是聚合后在图上显示的feature的style，并不是最开始添加到features数组里的style，所以用一个函数把当前显示的feature传进去：

````
function setClusterStyle(feature){
    	var features = feature.get('features');
    	var size = features.length;
    	var style = new ol.style.Style({
    		image: new ol.style.Circle({
    			radius: 18,
    			stroke: new ol.style.Stroke({
    				color: '#fff'
    			}),
    			fill: new ol.style.Fill({
    				color: '#3399CC'
    			})
    		}),
    		text: new ol.style.Text({
    			font: '15px sans-serif',
    			text: size.toString(),
    			fill: new ol.style.Fill({
    				color: '#fff'
    			})
    		})
    	});
    	return style;
    }
````

*feature.get('features')这一步得到的是feature所在的集群的feature数组。如果集群的聚合点只需要显示总共包含多个标记点，那么这里就直接用size表示了，如果是其他自定义的信息，在聚合时需要判断feature，并且你需要在每一个点的feature创建时使用feature.set("key","value","boolean")的方式把你的自定义信息写入，size这里替换成你自己写的一个函数的调用，把这里获得的聚合点数组features传进去，函数里面对这个数组遍历，每一个item先判空，然后item.get("key")判空，没问题了就进行处理，比如你可以把features数组下的所有基本标记点上的数字加起来，函数最后return这个结果，在刚才的setClusterStyle里接收，text的text里显示，数字的话记得toString()。
那么，集群你就已经完成了，再把地图层和集群层clusters一起添加到你的map的layers里，就大功告成。*

---
### 2. 如果是动态数据的Cluster：

- 把新的点的feature添加到source里：

````
source.addFeature(feature);
````

- 然后刷新集群：

````
clusterSource.refresh();
````

**这样就实现了动态添加标记点。**

- 另外提一下，如果对地图添加了“选中”的交互，在缩放地图时，选中的feature不会消失，需要取消选中才能消失，可以通过给map加入分辨率变化监听的方式来移除选中的feature：

````
var clickSelect = new ol.interaction.Select({
    	style:function(feature) {
    		return setSelectStyle(feature);
    	}
    });

    map.addInteraction(clickSelect);
    map.getView().on('change:resolution',function(){
    	clickSelect.getFeatures().clear();
    });
````

---
### 3. 效果图：

![](https://github.com/13608089849/Openlayer3-Cluster/blob/master/image/cluster.png)


---

**关于Cluster的心得就说这么多，下一章说说[LineString](https://github.com/13608089849/Openlayer3-LineString)的静态和动态使用。**

