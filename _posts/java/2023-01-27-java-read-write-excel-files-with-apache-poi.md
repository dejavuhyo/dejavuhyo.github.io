---
title: Java Apache POI로 엑셀 파일 읽기 및 쓰기
author: dejavuhyo
date: 2023-01-27 18:45:00 +0900
categories: [Language, Java]
tags: [read-excel, write-excel, java-poi, java-excel, java-xlsx, apache-poi, excel-hssf, excel-xssf, excel-xssf]
---

## 1. Apache POI
[Apache POI](https://poi.apache.org/)는 Excel 파일과 관련된 내용을 처리하기 위해 다른 많은 오픈 소스 라이브러리 중에서 신뢰할 수 있는 라이브러리이다. 또한 Apache POI 라이브러리를 사용하여 MS Word 및 MS PowerPoint 파일을 읽고 쓸 수 있다.

## 2. POI 라이브러리의 중요 클래스

### 1) HSSF, XSSF 및 XSSF 클래스

* HSSF: Excel 97(-2007) 파일 형식의 POI 프로젝트 Java 구현이다. 예: [HSSFWorkbook](https://poi.apache.org/apidocs/dev/org/apache/poi/hssf/usermodel/HSSFWorkbook.html), [HSSFSheet](https://poi.apache.org/apidocs/dev/org/apache/poi/hssf/usermodel/HSSFSheet.html).

* XSSF: Excel 2007 OOXML(.xlsx) 파일 형식의 POI 프로젝트 Java 구현이다. 예: [XSSFWorkbook](https://poi.apache.org/apidocs/dev/org/apache/poi/xssf/usermodel/XSSFWorkbook.html), [XSSFSheet](https://poi.apache.org/apidocs/dev/org/apache/poi/xssf/usermodel/XSSFSheet.html).

* SXSSF (3.8-beta3부터): 거대한 스프레드시트를 생성해야 하고 힙 공간이 제한되어 있을 때 사용되는 XSSF의 API 호환 스트리밍 확장이다. 예: [SXSSFWorkbook](https://poi.apache.org/apidocs/dev/org/apache/poi/xssf/streaming/SXSSFWorkbook.html), [SXSSFSheet](https://poi.apache.org/apidocs/dev/org/apache/poi/xssf/streaming/SXSSFSheet.html). SXSSF는 슬라이딩 윈도우 내의 행에 대한 액세스를 제한하여 메모리 사용량을 줄이는 반면 XSSF는 문서의 모든 행에 대한 액세스를 제공한다.

### 2) 행 및 셀
위의 클래스와 별도로 [Row](https://poi.apache.org/apidocs/dev/org/apache/poi/ss/usermodel/Row.html) 및 [Cell](https://poi.apache.org/apidocs/dev/org/apache/poi/ss/usermodel/Cell.html)은 Excel 시트의 특정 행 및 특정 셀과 상호 작용한다.

### 3) 스타일링 관련 클래스
[CellStyle](https://poi.apache.org/apidocs/dev/org/apache/poi/ss/usermodel/CellStyle.html), [BuiltinFormats](https://poi.apache.org/apidocs/dev/org/apache/poi/ss/usermodel/BuiltinFormats.html), [ComparisonOperator](https://poi.apache.org/apidocs/dev/org/apache/poi/ss/usermodel/ComparisonOperator.html), [ConditionalFormattingRule](https://howtodoinjava.com/java/library/readingwriting-excel-files-in-java-poi-tutorial/), [FontFormatting](https://poi.apache.org/apidocs/dev/org/apache/poi/ss/usermodel/FontFormatting.html), [IndexedColors](https://poi.apache.org/apidocs/dev/org/apache/poi/ss/usermodel/IndexedColors.html), [PatternFormatting](https://poi.apache.org/apidocs/dev/org/apache/poi/hssf/record/cf/PatternFormatting.html), [SheetConditionalFormatting](https://poi.apache.org/apidocs/dev/org/apache/poi/ss/usermodel/SheetConditionalFormatting.html) 등과 같은 다양한 클래스는 주로 일부 규칙을 기반으로 시트에 서식을 추가해야 할 때 사용된다.

### 4) FormulaEvaluator
FormulaEvaluator는 Excel 시트의 수식 셀을 평가하는데 사용된다.

## 3. Maven Dependency

* pom.xml

```xml
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>5.2.3</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>5.2.3</version>
</dependency>
```

## 4. Excel 파일 읽기

```java
import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.util.IOUtils;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

import java.io.FileInputStream;
import java.util.Iterator;

public class ReadExcel {
    public static void main(String[] args) {
        try {
            FileInputStream file = new FileInputStream("c:/temp/excel_demo.xlsx");
            IOUtils.setByteArrayMaxOverride(Integer.MAX_VALUE);

            //Create Workbook instance holding reference to .xlsx file
            XSSFWorkbook workbook = new XSSFWorkbook(file);

            //Get first/desired sheet from the workbook
            XSSFSheet sheet = workbook.getSheetAt(0);

            //Iterate through each rows one by one
            Iterator<Row> rowIterator = sheet.iterator();
            while (rowIterator.hasNext()) {
                Row row = rowIterator.next();
                //For each row, iterate through all the columns
                Iterator<Cell> cellIterator = row.cellIterator();

                while (cellIterator.hasNext()) {
                    Cell cell = cellIterator.next();
                    //Check the cell type and format accordingly
                    switch (cell.getCellType()) {
                        case NUMERIC:
                            System.out.print(cell.getNumericCellValue() + "\t");
                            break;
                        case STRING:
                            System.out.print(cell.getStringCellValue() + "\t");
                            break;
                        default:
                            throw new IllegalStateException("Unexpected value: " + cell.getCellType());
                    }
                }
                System.out.println("");
            }
            file.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## 5. Excel 파일 쓰기

```java
import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

import java.io.FileOutputStream;
import java.net.URL;
import java.util.Map;
import java.util.Set;
import java.util.TreeMap;

public class WriteExcel {
    public static void main(String[] args) {
        //Blank workbook
        XSSFWorkbook workbook = new XSSFWorkbook();

        //Create a blank sheet
        XSSFSheet sheet = workbook.createSheet("Employee Data");

        //This data needs to be written (Object[])
        Map<String, Object[]> data = new TreeMap<String, Object[]>();
        data.put("1", new Object[]{"ID", "NAME", "LASTNAME"});
        data.put("2", new Object[]{1, "Amit", "Shukla"});
        data.put("3", new Object[]{2, "Lokesh", "Gupta"});
        data.put("4", new Object[]{3, "John", "Adwards"});
        data.put("5", new Object[]{4, "Brian", "Schultz"});

        //Iterate over data and write to sheet
        Set<String> keyset = data.keySet();
        int rownum = 0;
        for (String key : keyset) {
            Row row = sheet.createRow(rownum++);
            Object[] objArr = data.get(key);
            int cellnum = 0;
            for (Object obj : objArr) {
                Cell cell = row.createCell(cellnum++);
                if (obj instanceof String)
                    cell.setCellValue((String) obj);
                else if (obj instanceof Integer)
                    cell.setCellValue((Integer) obj);
            }
        }
        try {
            //Write the workbook in file system
            URL url = ReadExcel.class.getClassLoader().getResource("excel_demo.xlsx");
            FileOutputStream out = new FileOutputStream(url.getFile());
            workbook.write(out);
            out.close();

            System.out.println("excel_demo.xlsx written successfully on disk.");

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## [출처 및 참고]
* [https://howtodoinjava.com/java/library/readingwriting-excel-files-in-java-poi-tutorial/](https://howtodoinjava.com/java/library/readingwriting-excel-files-in-java-poi-tutorial/)
* [https://github.com/lokeshgupta1981/opensource-examples/blob/main/src/main/java/com/howtodoinjava/demo/poi/AppendToExcel.java](https://github.com/lokeshgupta1981/opensource-examples/blob/main/src/main/java/com/howtodoinjava/demo/poi/AppendToExcel.java)
