#### 20190104

- 所有公共属性的key值都要求增加`_`前缀，如果key的前缀为若干个`$`或者`_`符号都会统一替换为一个`_`作为前缀

#### 20190103

- 增加了多个关于用户信息的字段： nickName、avatarUrl、gender、country、province、city、brand。
- 增加`isDefaultTrack`字段，用于后端判断事件是否为自定义事件
- 对自定义事件中传入的event_name和参数值做格式化转换，如果event_name，和参数的key前缀有$符号，自动将其替换掉

#### 20181214

- 增加了$wxVersion字段，用于说明微信版本号

#### 20181119

- 将返回值$scene和$latestScene从之前的场景值说明替换为场景值参数
- 增加经纬度信息字段$latitude、$longitude、$speed和$speed


