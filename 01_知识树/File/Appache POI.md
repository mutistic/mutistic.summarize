## Apache POI
> Apache POI api的主要用途是用于文本提取应用程序，例如网络蜘蛛，索引构建器和内容管理系统

```
  Apache POI项目的任务是创建和维护Java API，以基于Office Open XML标准（OOXML）和Microsoft的OLE 2复合文档格式（OLE2）来处理各种文件格式。
  
  简而言之，可以使用Java读写MS Excel文件。此外，可以使用Java读写MS Word和MS PowerPoint文件。
  
  Apache POI是您的Java Excel解决方案（适用于Excel 97-2008）
```
支持功能：
```
该库包括以下组件，大致按照成熟度从高到低的顺序排列：
    Excel电子表格（通用SS = HSSF，XSSF和SXSSF）
    PowerPoint幻灯片（普通SL = HSLF和XSLF）
    文字处理文档（通用WP = HWPF和XWPF）
    Outlook电子邮件（HSMF和HMEF）
    Visio图（HDGF和XDGF）
    发布者（HPBF）

以及较低级别的支持组件：
    OLE2文件系统（POIFS）
    OLE2文档属性（HPSF）
    TNEF（HMEF）用于Outlook winmail.dat文件
    OpenXML4J（OOXML）
```

DOCUMENT | URL
---|---
Apache POI 指南 |[https://poi.apache.org](https://poi.apache.org)  
Apache POI github | [https://github.com/apache/poi](https://github.com/apache/poi)  
Apache POI api | [https://poi.apache.org/apidocs/dev/overview-summary.html](https://poi.apache.org/apidocs/dev/overview-summary.html)
Apache POI 特性 | [https://poi.apache.org/changes.html](https://poi.apache.org/changes.html)

## Apache POI 引入方式
> 201910最新版本 4.1.1
#### Apache Maven：Apache POI

```xml
<dependency>
  <groupId>org.apache.poi</groupId>
  <artifactId>poi</artifactId>
  <version>4.1.1</version>
</dependency>
```

## Excel读写-基于注解方式实现
### 1、注解相关类
ExcelSheet.java
```Java
package com.core.datac.file.annotation;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 * Excel Sheet 注解
 * <p>1、作用于类
 */
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface ExcelSheet {
  // 标签页
  String name();
}
```
ExcelColumn.java
```Java
package com.core.data.file.annotation;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
/**
 * excel 列 注解
 * <p> 1、作用于字段
 */
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface ExcelColumn {
  // 列名
  String name();
  // 排序
  int sort() default -1;
  // 格式化规则
  String format() default "";
  // 字符长度 > 默认列宽=字符长度*20
  int length() default -1;
  // 所属组
  String groupName() default "";
}
```

### 2、工具类相关：
FileTypeEnum.java
```Java
package com.core.data.file.constants;
// 文件类型 枚举
public enum FileTypeEnum {
  // HSSF：POI操作Excel97-2003版本 > xls文件
  HSSF("xls", ".xls"),
  // XSSF：POI操作Excel2007版本开始 > xlsx文件
  XSSF("xlsx", ".xlsx"),
  // SXSSF：POI针对大数据量 > xlsx文件
  SXSSF("xlsx", ".xlsx"),
  // CSV > csv文件
  CSV("csv", ".csv"),  ;
  FileTypeEnum(String key, String value) {
    this.key = key;
    this.value = value;
  }
  private String key;
  private String value;
  public String getKey() {
    return key;
  }
  public String getValue() {
    return value;
  }
  public static FileTypeEnum getEnum(String key) {
    for (FileTypeEnum temp : values()) {
      if (temp.getKey().equals(key)) {
        return temp;
      }
    }
    return null;
  }
}

```
FileConstant.java
```
package com.core.data.file.constants;
import com.core.data.file.utils.ExcelColumnComparator;
// 文件 常量
public class FileConstant {
  // 点 .
  public static final String POINT = ".";
  // Excel Column 排序规则
  public static final ExcelColumnComparator COMPARATOR_EXCEL_COLUMN = new ExcelColumnComparator();
}
```
ExcelColumnComparator.java
```Java
package com.core.data.file.utils;
import com.core.data.file.annotation.ExcelColumn;
import java.util.Comparator;
// Excel Column Comparator 排序规则
public class ExcelColumnComparator implements Comparator<ExcelColumn> {
  @Override
  public int compare(ExcelColumn column1, ExcelColumn column2) {
    return column1.sort() - column2.sort();
  }
}
```
ExcelUtil.java
```Java
package com.core.data.file.utils;

import com.core.data.file.annotation.ExcelColumn;
import com.core.data.file.annotation.ExcelSheet;
import com.core.data.file.constants.FileConstant;
import com.core.data.file.constants.FileTypeEnum;
import java.lang.reflect.Field;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import org.apache.commons.lang3.StringUtils;

// Excel工具类
public class ExcelUtil {

  public static FileTypeEnum getFileEnum(String fileName) {
    if (StringUtils.isBlank(fileName) || !fileName.contains(FileConstant.POINT)) {
      return null;
    }
    String suffix = fileName.substring(fileName.lastIndexOf(FileConstant.POINT) + 1);
    return FileTypeEnum.getEnum(suffix);
  }

  /**
   * 获取 Excel 页签名
   *
   * @param clazz 包含ExcelSheet注解的类
   * @param <T>   类类型
   * @return 页签名
   */
  public static <T> String getSheetName(Class<T> clazz) {
    if (clazz == null) {
      return null;
    }
    ExcelSheet sheet = clazz.getAnnotation(ExcelSheet.class);
    if (sheet == null && StringUtils.isEmpty(sheet.name())) {
      return null;
    }
    return sheet.name();
  }

  /**
   * 获取 Excel Column与列注解的关联Map
   * <p> key=ExcelColumn，value=Filed
   *
   * @param <T>   类类型
   * @param clazz 包含ExcelColumn注解的类
   * @return 关联Map
   */
  public static <T> Map<ExcelColumn, Field> getColumnMap(Class<T> clazz) {
    if (clazz == null) {
      return null;
    }

    Field[] fieldArray = clazz.getDeclaredFields();
    Map<ExcelColumn, Field> columnMap = new HashMap<>(fieldArray.length);
    List<Map.Entry<ExcelColumn, Field>> columnList = new ArrayList<>();
//
//
//    Map<ExcelColumn, Field> columnMap = new HashMap<>(fieldArray.length);
//    List<ExcelColumn> columnList = new ArrayList<>();
//    ExcelColumn excelField;
//    for (Field field : fieldArray) {
//      excelField = field.getAnnotation(ExcelColumn.class);
//      if (excelField != null) {
//        columnMap.put(excelField, field);
//        columnList.add(excelField);
//      }
//    }
//    if (CommonUtils.isEmpty(columnList)) {
//      return columnMap;
//    }
//    Collections.sort(columnList, FileConstant.COMPARATOR_EXCEL_COLUMN);
//    Map<ExcelColumn, Field> filedMap = new LinkedHashMap<>(columnList.size());
//    for (ExcelColumn column : columnList) {
//      filedMap.put(column, columnMap.get(column));
//    }
    return columnMap;
  }

  public static List<ExcelColumn> getExcelColumnList(List<String> headList) {
    List<ExcelColumn> columnList = new ArrayList<>();
    return columnList;
  }
}
```

### 3、读Excel
AbstractReadExcel.java
```Java
package com.core.data.file.excel;

import com.core.data.file.constants.FileTypeEnum;
import java.io.IOException;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;
import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.ss.usermodel.Sheet;
import org.apache.poi.ss.usermodel.Workbook;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.springframework.web.multipart.MultipartFile;

/**
 * 读取 Excel 抽象类
 */
public abstract class AbstractReadExcel {

  /**
   * Workbook
   */
  public Workbook workbook;
  /**
   * 输入流
   */
  public InputStream inputStream;

  /**
   * 获取实例
   *
   * @param multipartFile multipartFile
   * @return AbstractExcelRead 实例
   * @throws IOException IOException
   */
  public abstract AbstractReadExcel getInstance(MultipartFile multipartFile, FileTypeEnum fileType)
      throws IOException;

  /**
   * 初始化 Workbook
   *
   * @param fileType 文件类型枚举
   * @throws IOException IOException
   */
  public void initWorkbook(FileTypeEnum fileType) throws IOException {
    if (FileTypeEnum.HSSF == fileType) {
      this.workbook = new HSSFWorkbook(this.inputStream);
    } else if (FileTypeEnum.XSSF == fileType) {
      this.workbook = new XSSFWorkbook(this.inputStream);
    }
  }

  /**
   * 读取Excel
   *
   * @param multipartFile 文件
   * @param clazz         类
   * @param <T>           类类型
   * @return 读取结果
   */
  public abstract <T> List<T> read(MultipartFile multipartFile, Class<T> clazz);

  /**
   * 并行读取Excel
   *
   * @param multipartFile 文件
   * @param clazz         类
   * @param <T>           类类型
   * @return 读取结果
   */
  public abstract <T> List<T> parallelRead(MultipartFile multipartFile, Class<T> clazz);

  public List<String> getHeadList(Sheet sheet) {
    if (sheet.getLastRowNum() == 0) {
      return null;
    }
    Row head = sheet.getRow(0);
    if (head.getLastCellNum() == 0) {
      return null;
    }
    List<String> headList = new ArrayList<>();
    Cell cell;
    for (int i = 0; i < head.getLastCellNum(); i++) {
      cell = head.getCell(i);
      if (cell == null) {
        headList.add("null-" + i);
        continue;
      }
      headList.add(cell.getStringCellValue());
    }

    return headList;
  }

  /**
   * 关闭资源
   *
   * @param inputStream 输入流
   * @throws IOException IOException
   */
  public void close(InputStream inputStream) throws IOException {
    if (this.workbook != null) {
      this.workbook.close();
    }
    if (inputStream != null) {
      inputStream.close();
    }
  }

}
```
AnnotationReadExcel.java
```Java
package com.core.data.file.excel.function;

import ch.qos.logback.core.util.FileUtil;
import com.core.data.file.annotation.ExcelColumn;
import com.core.data.file.constants.FileTypeEnum;
import com.core.data.file.excel.AbstractReadExcel;
import com.core.data.file.utils.ExcelUtil;
import com.core.data.utils.CommonUtils;
import java.io.IOException;
import java.lang.reflect.Field;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import lombok.extern.slf4j.Slf4j;
import org.apache.poi.ss.usermodel.Sheet;
import org.springframework.web.multipart.MultipartFile;

/**
 * 通过注解读取 Excel
 */
@Slf4j
public class AnnotationReadExcel extends AbstractReadExcel {

  /**
   * Excel 列关系集合
   */
  private Map<ExcelColumn, Field> columnMap;

  @Override
  public AbstractReadExcel getInstance(MultipartFile multipartFile, FileTypeEnum fileType)
      throws IOException {
    AbstractReadExcel excelRead = new AnnotationReadExcel();
    excelRead.inputStream = multipartFile.getInputStream();
    excelRead.initWorkbook(fileType);
    return excelRead;
  }

  @Override
  public <T> List<T> read(MultipartFile multipartFile, Class<T> clazz) {
    List<T> dataList = new ArrayList<>();
    columnMap = ExcelUtil.getColumnMap(clazz);
    if (CommonUtils.isEmpty(columnMap)) {
      return dataList;
    }
    //默认读取第一个sheet
    for (int i = 1; i <= workbook.getNumberOfSheets(); i++) {
      Sheet sheet = workbook.getSheetAt(i - 1);
//      List<ExcelColumn> columnList = FileUtil.getExcelColumnList(columnMap.keySet(), super.getHeadList(sheet));
    }

    return dataList;
  }

  @Override
  public <T> List<T> parallelRead(MultipartFile multipartFile, Class<T> clazz) {
    return null;
  }
}
```

### 4、写Excel