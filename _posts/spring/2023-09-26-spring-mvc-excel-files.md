---
title: Spring MVC Excel 파일 업로드 및 표시
author: dejavuhyo
date: 2023-09-26 07:00:00 +0900
categories: [Framework, Spring]
tags: [spring-excel, excel-upload, 스프링-엑셀, 엑셀-업로드]
---

## 1. 엑셀 파일 업로드
파일을 업로드할 수 있도록 먼저 MultipartFile을 수신 하고 이를 현재 위치에 저장하는 컨트롤러 매핑을 생성한다.

```java
private String fileLocation;

@PostMapping("/uploadExcelFile")
public String uploadFile(Model model, MultipartFile file) throws IOException {
    InputStream in = file.getInputStream();
    File currDir = new File(".");
    String path = currDir.getAbsolutePath();
    fileLocation = path.substring(0, path.length() - 1) + file.getOriginalFilename();
    FileOutputStream f = new FileOutputStream(fileLocation);
    int ch = 0;
    while ((ch = in.read()) != -1) {
        f.write(ch);
    }
    f.flush();
    f.close();
    model.addAttribute("message", "File: " + file.getOriginalFilename() 
      + " has been uploaded successfully!");
    return "excel";
}
```

다음으로, Excel 파일만 허용하도록 설정된 accept 속성을 갖는 파일 유형의 입력이 포함된 양식을 사용하여 JSP 파일을 생성한다.

```jsp
<c:url value="/uploadExcelFile" var="uploadFileUrl" />
<form method="post" enctype="multipart/form-data"
  action="${uploadFileUrl}">
    <input type="file" name="file" accept=".xls,.xlsx" /> <input
      type="submit" value="Upload file" />
</form>
```

## 2. 엑셀 파일 읽기
업로드된 Excel 파일을 구문 분석하기 위해 `.xls` 및 `.xlsx` 파일 모두에서 작동할 수 있는 Apache POI 라이브러리를 사용한다.

콘텐츠 및 서식과 관련된 Excel 셀의 속성을 포함하는 MyCell 이라는 도우미 클래스를 만든다.

```java
public class MyCell {
    private String content;
    private String textColor;
    private String bgColor;
    private String textSize;
    private String textWeight;

    public MyCell(String content) {
        this.content = content;
    }
    
    //standard constructor, getters, setters
}
```

Excel 파일의 내용을 MyCell 개체 목록이 포함된 맵으로 읽는다.

### 1) .xls 파일 구문 분석
`.xls` 파일은 Apache POI 라이브러리에서 HSSFSheet 개체로 구성된 HSSFWorkbook 클래스로 표시된다. `.xls` 파일의 내용을 열고 읽으려면 [Java에서 Microsoft Excel 사용](https://www.baeldung.com/java-microsoft-excel)을 참고한다.

셀 서식을 구문 분석하기 위해 배경색 및 글꼴과 같은 속성을 결정하는데 도움이 될 수 있는 HSSFCellStyle 개체를 얻는다. 모든 읽기 속성은 MyCell 개체의 속성에 설정된다.

```java
HSSFCellStyle cellStyle = cell.getCellStyle();

MyCell myCell = new MyCell();

HSSFColor bgColor = cellStyle.getFillForegroundColorColor();
if (bgColor != null) {
    short[] rgbColor = bgColor.getTriplet();
    myCell.setBgColor("rgb(" + rgbColor[0] + ","
      + rgbColor[1] + "," + rgbColor[2] + ")");
    }
HSSFFont font = cell.getCellStyle().getFont(workbook);
```

색상은 JSP 페이지에서 CSS를 사용하여 더 쉽게 표시할 수 있도록 `rgb(rVal, gVal, bVal)` 형식으로 읽혀진다.

글꼴 크기, 두께, 색상도 알아본다.

```java
myCell.setTextSize(font.getFontHeightInPoints() + "");
if (font.getBold()) {
    myCell.setTextWeight("bold");
}
HSSFColor textColor = font.getHSSFColor(workbook);
if (textColor != null) {
    short[] rgbColor = textColor.getTriplet();
    myCell.setTextColor("rgb(" + rgbColor[0] + ","
      + rgbColor[1] + "," + rgbColor[2] + ")");
}
```

### 2) .xlsx 파일 구문 분석
최신 `.xlsx` 형식의 파일의 경우 XSSFWorkbook 클래스 및 통합 문서 내용에 유사한 클래스를 사용할 수 있으며 [Java에서 Microsoft Excel 사용](https://www.baeldung.com/java-microsoft-excel) 문서에도 설명되어 있다.

`.xlsx` 형식의 셀 서식을 읽는 방법은, 먼저 셀과 연결된 XSSFCellStyle 개체를 검색하고 이를 사용하여 배경색과 글꼴을 결정한다.

```java
XSSFCellStyle cellStyle = cell.getCellStyle();

MyCell myCell = new MyCell();
XSSFColor bgColor = cellStyle.getFillForegroundColorColor();
if (bgColor != null) {
    byte[] rgbColor = bgColor.getRGB();
    myCell.setBgColor("rgb(" 
      + (rgbColor[0] < 0 ? (rgbColor[0] + 0xff) : rgbColor[0]) + ","
      + (rgbColor[1] < 0 ? (rgbColor[1] + 0xff) : rgbColor[1]) + ","
      + (rgbColor[2] < 0 ? (rgbColor[2] + 0xff) : rgbColor[2]) + ")");
}
XSSFFont font = cellStyle.getFont();
```

이 경우 색상의 RGB 값은 부호 있는 바이트 값이 되므로 음수 값에 `0xff`를 더해 부호 없는 값을 얻는다.

글꼴의 속성도 결정한다.

```java
myCell.setTextSize(font.getFontHeightInPoints() + "");
if (font.getBold()) {
    myCell.setTextWeight("bold");
}
XSSFColor textColor = font.getXSSFColor();
if (textColor != null) {
    byte[] rgbColor = textColor.getRGB();
    myCell.setTextColor("rgb("
      + (rgbColor[0] < 0 ? (rgbColor[0] + 0xff) : rgbColor[0]) + "," 
      + (rgbColor[1] < 0 ? (rgbColor[1] + 0xff) : rgbColor[1]) + "," 
      + (rgbColor[2] < 0 ? (rgbColor[2] + 0xff) : rgbColor[2]) + ")");
}
```

### 3) 빈 행 처리
위에서 설명한 방법은 Excel 파일의 빈 행을 고려하지 않는다. 빈 행도 표시하는 파일의 충실한 변환을 원한다면 빈 문자열을 콘텐츠로 포함하는 MyCell 개체의 ArrayList를 사용하여 결과 HashMap에서 이를 시뮬레이션해야 한다.

처음에 Excel 파일을 읽은 후 파일의 빈 행은 크기가 0인 ArrayList 개체가 된다.

추가해야 하는 빈 String 개체 수를 결정하기 위해 먼저 maxNrCols 변수를 사용하여 Excel 파일에서 가장 긴 행을 결정한다. 그런 다음 크기가 0인 HashMap의 모든 목록에 빈 String 개체 수를 추가한다.

```java
int maxNrCols = data.values().stream()
  .mapToInt(List::size)
  .max()
  .orElse(0);

data.values().stream()
  .filter(ls -> ls.size() < maxNrCols)
  .forEach(ls -> {
      IntStream.range(ls.size(), maxNrCols)
        .forEach(i -> ls.add(new MyCell("")));
  });
```

## 3. 엑셀 파일 표시
Spring MVC를 사용하여 읽은 Excel 파일을 표시하려면 컨트롤러 매핑과 JSP 페이지를 정의해야 한다.

### 1) 스프링 MVC 컨트롤러
업로드된 파일의 내용을 읽기 위해 위의 코드를 호출하는 `@RequestMapping` 메소드를 생성한 다음 반환된 맵을 모델 속성으로 추가한다.

```java
@Resource(name = "excelPOIHelper")
private ExcelPOIHelper excelPOIHelper;

@RequestMapping(method = RequestMethod.GET, value = "/readPOI")
public String readPOI(Model model) throws IOException {

  if (fileLocation != null) {
      if (fileLocation.endsWith(".xlsx") || fileLocation.endsWith(".xls")) {
          Map<Integer, List<MyCell>> data
            = excelPOIHelper.readExcel(fileLocation);
          model.addAttribute("data", data);
      } else {
          model.addAttribute("message", "Not a valid excel file!");
      }
  } else {
      model.addAttribute("message", "File missing! Please upload an excel file.");
  }
  return "excel";
}
```

### 2) JSP
파일 내용을 시각적으로 표시하기 위해 HTML 테이블을 만들고 각 테이블 셀의 스타일 속성에 Excel 파일의 각 셀에 해당하는 서식 속성을 추가한다.

```jsp
<c:if test="${not empty data}">
    <table style="border: 1px solid black; border-collapse: collapse;">
        <c:forEach items="${data}" var="row">
            <tr>
                <c:forEach items="${row.value}" var="cell">
                    <td style="border:1px solid black;height:20px;width:100px;
                      background-color:${cell.bgColor};color:${cell.textColor};
                      font-weight:${cell.textWeight};font-size:${cell.textSize}pt;">
                      ${cell.content}
                    </td>
                </c:forEach>
            </tr>
        </c:forEach>
    </table>
</c:if>
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-excel-files](https://www.baeldung.com/spring-mvc-excel-files)
