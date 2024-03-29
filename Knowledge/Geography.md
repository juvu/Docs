# 地理


* 非洲板块、印澳板块与欧亚板块碰:导致整个欧亚大陆南侧全线隆起
    - 一众超级山脉及高原就此诞生 堪称地球2亿年来最壮观的造山运动 它西起大西洋东岸 东至印度尼西亚岛弧 包括阿尔卑斯山、喜马拉雅山 伊朗高原、青藏高原等等 全长超过1万千米 横贯地球、直刺苍穹 地质学上称之为 阿尔卑斯-喜马拉雅造山带

## 经纬度

* 纬线：长度是赤道的周长乘以纬线的纬度的余弦，向北和向南，各分90°，称为北纬和南纬，分别用“N”和“S”表示
* 经度：过某地的经线面与本初子午面所成的二面角
    - 格林尼治的子午线被正式定为经度的起点（经度是0°），东经180°即西经180°

## 青藏高原

* 印度洋板块冲击亚欧板块形成
* 抽风机效果：更多的太阳辐射，热空气上升，形成低气压带
    - 南亚季风
    - 东亚季风：分成高寒、干旱区、季风区

## 摩尔多瓦


## 安纳普尔纳峰


### 科尔沁草原

* 但若要说扎根于此、立国于此、鼎盛于此的 便只有契丹
* 早在南北朝时期 契丹便在内蒙古东南部 西拉木伦河及老哈河流域悄然兴起

我生于斯，长于斯。四十余年间，见贯这里的晓月晨风，朝云暮雨，晨曦暮韵，空山林谷，千里碧野，万倾戈壁，寥落星河，在这里农耕与游牧协作，雄浑纤秾并肩，千百世以来，殷殷循循生息。地可视一人之角，天可视一人之边，无限仅限人心大小，距离仅限心意远近。胡琴伴长调咏叹，烧酒对炙肉饕餮。牛羊入圈，马放南山，但得一人之落日天涯。

极目山川无尽头，风烟不断水长流
如何造物开天地，到此令人放马牛
——丘处机。

## Geohash

* 将地球理解为一个二维平面，将平面递归分解成更小的子块，每个子块在一定经纬度范围内拥有相同的编码，
* 纬度范围 (-90, 90) 平分成两个区间 (-90,0)、(0, 90)。如果目标纬度位于前一个区间，则编码为0，否则编码为1。
* 将 (0, 90) 分成 (0, 45), (45, 90)两个区间
    - 经度每隔0.00001度，距离相差约1米；
    - 每隔0.0001度，距离相差约10米；
    - 每隔0.001度，距离相差约100米；
    - 每隔0.01度，距离相差约1000米；
    - 每隔0.1度，距离相差约10000米。
* 经度也用同样的算法，对 (-180, 180) 依次细分
* 将经度和纬度的编码合并，奇数位是纬度，偶数位是经度。用0-9、b-z（去掉a, i, l, o）这32个字母进行 base32 编码,数据库新增一个字段 geohash，记录此点的geohash值
* 查询附近的时候，利用SQL中 like 'wx4g0e%' 进行查询
* 获取附近点距离

```php
/**
 * [PHP Code] 根据经纬度计算两点之间的记录
 * @param $lat1 纬度1
 * @param $lng1 经度1
 * @param $lat2 纬度2
 * @param $lng2 经度2
 * @return float 单位(米)
 */
function getDistance($lat1, $lng1, $lat2, $lng2)
{
    //地球半径
    $R = 6378137;

    //将角度转为弧度
    $radLat1 = deg2rad($lat1);
    $radLat2 = deg2rad($lat2);
    $radLng1 = deg2rad($lng1);
    $radLng2 = deg2rad($lng2);

    //结果
    $s = acos(cos($radLat1) * cos($radLat2) * cos($radLng1 - $radLng2) + sin($radLat1) * sin($radLat2)) * $R;

    //精度
    $s = round($s * 10000)/10000;

    return  round($s);
}
```

* [helei112g/laravel_geohash](https://github.com/helei112g/laravel_geohash):在laravel中使用geohash实现附近的功能
