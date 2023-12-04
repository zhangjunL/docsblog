
  #·.Net Core拆分execl文件
  ## 简介
  ### 一、用途
    这是一个.net core写的按照指定行数拆分execl文件的例子，可以将一个execl文件按照指定行数和区域，将execl文件拆分为多个execl文件，
    并且使用单元格的数据命名新的文件，在此示例中从A1到M39区域拆分为一个execl， A40:M78拆分为第二个execl文件，依此类推。
  ### 二、依赖
    使用了EPPlus库，这个库可以在nuget上下载到
  ## 代码

```cs
    static void SplitAndCopyStyles(string sourceFilePath, string targetDirectory)
    {
        ExcelPackage.LicenseContext = LicenseContext.NonCommercial;
        using (var sourcePackage = new ExcelPackage(new FileInfo(sourceFilePath)))
        {
            var sourceWorksheet = sourcePackage.Workbook.Worksheets[0];

            int rowsPerFile = 39; // 指定拆分的行数

            int totalRows = sourceWorksheet.Dimension.End.Row;

            for (int startRow = 1; startRow <= totalRows; startRow += rowsPerFile)
            {
                int endRow = Math.Min(startRow + rowsPerFile - 1, totalRows);

                // 创建一个新的ExcelPackage和工作表
                using (var targetPackage = new ExcelPackage())
                {
                    var targetWorksheet = targetPackage.Workbook.Worksheets.Add("Sheet1");
                    var sourceRange = sourceWorksheet.Cells["A"+ startRow+ ":M"+ endRow];
                    var targetRange = targetWorksheet.Cells["A1"];
                    sourceRange.Copy(targetRange);
                    for (int col = 1; col <= sourceWorksheet.Dimension.End.Column; col++)
                    {
                        targetWorksheet.Column(col).Width = sourceWorksheet.Column(col).Width;
                    }
                    string continent = targetWorksheet.Cells["C2"].Text;
                    string country = targetWorksheet.Cells["F2"].Text;
                    string name = targetWorksheet.Cells["E3"].Text;
                    
                    // 保存新的Excel文件
                    string newFileName = $"{continent}_{country}_{name}.xlsx";
                    newFileName=CleanFileName(newFileName);
                    string targetFilePath = Path.Combine(targetDirectory, newFileName);

                    // 确保目录存在
                    Directory.CreateDirectory(targetDirectory);

                    targetPackage.SaveAs(new FileInfo(targetFilePath));
                }
            }
        }
    }

```