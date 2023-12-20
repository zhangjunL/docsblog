# Magicodes.IE使用
## 导入
```
        [HttpPost("/Import/Import")]
        [AllowAnonymous]
        public virtual async Task Import(IFormFile file)
        {
            var path = Path.Combine(Path.GetTempPath(), $"{YitIdHelper.NextId()}.xlsx");
            await using (var stream = File.Create(path))
            {
                await file.CopyToAsync(stream);
            }
            // 创建Excel导入对象
            IImporter importer = new ExcelImporter();
            var import =await  importer.Import<ImportDto>(path);

            if (import == null)
                throw Oops.Oh("导入模版解析异常");
            if (import.Exception != null)
                throw Oops.Oh("导入异常:" + import.Exception);
            if (import.RowErrors.Count > 0)
                throw Oops.Oh("数据校验:" + JSON.Serialize(import.RowErrors));


            // await ImportDtoMapper(import);
        }
```
## 导入模板下载

```
        /// <summary>
        /// 导入模板下载
        /// </summary>
        /// <returns></returns>
        [HttpGet("/Import/importTemplate")]
        [AllowAnonymous]
        public virtual async Task<FileContentResult> ImportTemplate()
        {
            // 创建Excel导入对象
            IImporter importer = new ExcelImporter();
            var byteArray = await importer.GenerateTemplateBytes<ImportDto>();

            // 文件名称
            var fileName = typeof(ImportDto).GetDescriptionValue<CommentAttribute>().Comment + "导入模版.xlsx";

            return await Task.FromResult(
                new FileContentResult(byteArray, "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet")
                {
                    FileDownloadName = System.Web.HttpUtility.UrlEncode(fileName, System.Text.Encoding.UTF8)
                });
        }
```