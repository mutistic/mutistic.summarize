## MyBatis Plus
> MyBatis-Plus（简称 MP）是一个 MyBatis 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生
><br/>作者是国内开发员baomidou的开源项目
	
DOCUMENT | URL
---|---
MyBatis Plus 指南 |[https://mybatis.plus/guide/](https://mybatis.plus/guide/)
MyBatis Plus github |[https://github.com/baomidou/mybatis-plus](https://github.com/baomidou/mybatis-plus)

## MyBatis Plus 引入方式
> 201911最新版本 3.2.0
#### Apache Maven：MyBatis Plus
```xml
<dependency>
  <groupId>com.baomidou</groupId>
  <artifactId>mybatis-plus</artifactId>
  <version>3.2.0</version>
</dependency>
```

#### Apache Maven：MBbatis Plus Boot Starter
```xml
<dependency>
 <groupId>com.baomidou</groupId>
 <artifactId>mybatis-plus-boot-starter</artifactId>
 <version>3.2.0</version>
</dependency>
```

## 在Spring Boot简单使用MyBatis Plus
1、数据库表 ddl：
```sql
CREATE TABLE `goods_price` (
 `id_` int(12) NOT NULL COMMENT '主键ID',
 `goods_sku` varchar(64) NOT NULL COMMENT '商品SKU',
 `goods_name` varchar(255) NOT NULL COMMENT '商品名称',
 `goods_price` varchar(12) NOT NULL COMMENT '商品价格',
 `goods_data` varchar(32) NOT NULL COMMENT '商品时间',
 `version_no` int(3) unsigned DEFAULT '0' COMMENT '版本号',
 PRIMARY KEY (`id_`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='商品价格趋势表';
```

2、Model：
```Java
package com.core.data.model;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.baomidou.mybatisplus.extension.activerecord.Model;
import lombok.Data;

@Data
@TableName("goods_price")
public class GoodsPrice extends Model {
 @TableId("id_")
 private Long id;
 @TableField("goods_sku")
 private String goodsSku;
 @TableField("goods_name")
 private String goodsName;
 @TableField("goods_price")
 private String goodsPrice;
 @TableField("goods_data")
 private String goodsData;
 @TableField("version_no")
 private Integer versionNo;
}
```

3、Mapper：
```Java
package com.core.data.mapper;
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.core.data.model.GoodsPrice;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface GoodsPriceMapper extends BaseMapper<GoodsPrice> {

}
```

4、Service：
```Java
// IGoodsPriceService 接口
package com.core.data.service;
import com.core.data.model.GoodsPrice;

public interface IGoodsPriceService {
 // 根据id查询方法
 GoodsPrice selectById(Long id);
}

// IGoodsPriceService impl 实现类
package com.core.data.service.impl;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.core.data.mapper.GoodsPriceMapper;
import com.core.data.model.GoodsPrice;
import com.core.data.service.IGoodsPriceService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class GoodsPriceServiceImpl extends ServiceImpl<GoodsPriceMapper, GoodsPrice>
  implements IGoodsPriceService {

 @Autowired
 private GoodsPriceMapper goodsPriceMapper;

 @Override
 public GoodsPrice selectById(Long id) {
  System.out.println("测试是否自动注入mapper：" + goodsPriceMapper);
  return super.baseMapper.selectById(id);
 }
}
```

5、Controller：
```Java
package com.core.data.controller;
import com.core.data.mapper.GoodsPriceMapper;
import com.core.data.model.GoodsPrice;
import com.core.data.service.IGoodsPriceService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/goodsPrice")
public class GoodsPriceController {

 @Autowired
 private IGoodsPriceService goodsPriceService;

 @GetMapping("/{id}")
 public GoodsPrice selectById(@PathVariable Long id){
  return goodsPriceService.selectById(id);
 }
}
```

6、开启扫描注解配置：
```Java
package com.core.data;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication(scanBasePackages = "com.core.data")
// 开启Mybatisplus mapper扫描注解
@MapperScan("com.baomidou.mybatisplus.samples.quickstart.mapper")
public class DataApplication {

	public static void main(String[] args) {
		SpringApplication.run(DataApplication.class, args);
	}

}
```

7、接口测试
```
Request :
Request URL: http://localhost:8080/snapshot/goodsPrice/1
Request Method: GET
Content-Type: application/json

Response：
{
  "id": 1,
  "goodsSku": "1",
  "goodsName": "1",
  "goodsPrice": "1",
  "goodsData": "1",
  "versionNo": 0
}
```