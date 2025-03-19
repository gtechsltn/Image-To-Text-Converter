# Image to Text Converter in C#
* [Online tool](https://www.imagetotext.info/)
* [ChatGPT](https://chatgpt.com/)
* [Convert Image to Text in ASPNet using C# and VB.NET on aspsnippets.com](https://www.aspsnippets.com/Articles/1526/Convert-Image-to-Text-in-ASPNet-using-C-and-VBNet/)
* [Tessdata](https://github.com/tesseract-ocr/tessdata)
* [ConsoleApp Template](https://github.com/gtechsltn/ConsoleApp)
* [Log4Net](https://github.com/gtechsltn/ConsoleApp/blob/master/ConsoleApp1/Src/App.config)
* [ConsoleApp in .NET (C#)](https://github.com/gtechsltn/ConsoleApp/blob/master/ConsoleApp1/Src/Program.cs)
* [Copy to Output Directory](https://dev.to/karenpayneoregon/visual-studio-copying-files-to-debug-or-release-folder-3onj)

# C# Converter Image to Text
* [ChatGPT](https://chatgpt.com/share/67da3994-35c4-8013-9308-b21cf8cc427a)

# C# Translate English to Vietnamese
* [ChatGPT](https://chatgpt.com/share/67da3994-35c4-8013-9308-b21cf8cc427a)

# Log4net (App.config)
```
﻿<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
  </configSections>
  <system.diagnostics>
    <trace autoflush="true">
      <listeners>
        <add name="textWriterTraceListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="logs\log4net.trace.txt" />
      </listeners>
    </trace>
  </system.diagnostics>
  <log4net>
    <appender name="ConsoleAppender" type="log4net.Appender.ConsoleAppender">
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%date [%thread] %-5level %logger [%ndc] &lt;%property{auth}&gt; - %message%newline" />
      </layout>
    </appender>
    <appender name="DailyRollingFileAppender" type="log4net.Appender.RollingFileAppender">
      <threshold value="ALL" />
      <file value="logs\traceroll.day.log" />
      <appendToFile value="true" />
      <rollingStyle value="Composite" />
      <datePattern value="yyyyMMdd" />
      <maximumFileSize value="10MB" />
      <maxSizeRollBackups value="-1" />
      <CountDirection value="1" />
      <preserveLogFileNameExtension value="true" />
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="[${COMPUTERNAME}] %d{ISO8601} %6r %-5p [%t] %c{2}.%M() - %m%n" />
      </layout>
    </appender>
    <root>
      <!-- ALL, DEBUG, INFO, WARN, ERROR, FATAL, OFF -->
      <level value="INFO" />
      <appender-ref ref="ConsoleAppender" />
      <appender-ref ref="DailyRollingFileAppender" />
    </root>
  </log4net>
</configuration>
```

# Log4net (Program.cs)
```
using log4net;
using System;
using System.Reflection;

namespace ImageToTextConverter.ConsoleApp
{
    class Program
    {
        private static readonly ILog log = LogManager.GetLogger(MethodBase.GetCurrentMethod().DeclaringType);
        static void Main()
        {
            log4net.Config.XmlConfigurator.Configure();
            log.Info("Hello World");
        }
    }
}
```

# TessData: 
* Download eng.traineddata, vie.traineddata
* From:
  * https://github.com/tesseract-ocr/tessdata
  * https://github.com/gtechsltn/tessdata
* Copy to output directory: **Copy always**

![Copy to output directory: **Copy always**](https://github.com/user-attachments/assets/8613c6ef-3f42-4f14-a8d8-6a74c5d5df36)

## IMPORTANT
```
In the file properties in Solution Explorer in Visual Studio, set:
Build action: None
Copy to output directory: Copy always
```

## Examples
```
MyProject/
├── bin/
├── tessdata/
│   ├── eng.traineddata
│   ├── other_lang.traineddata (optional)
├── Program.cs
```

# Console App
```
using System;
using Tesseract;

class Program
{
    static void Main()
    {
        string imagePath = "test-image.png";  // Change to your image path
        string tessDataPath = @"./tessdata";  // Folder containing .traineddata file

        try
        {
            using (var engine = new TesseractEngine(tessDataPath, "eng", EngineMode.Default))
            {
                using (var img = Pix.LoadFromFile(imagePath))
                {
                    using (var page = engine.Process(img))
                    {
                        Console.WriteLine("Extracted Text:");
                        Console.WriteLine(page.GetText());
                    }
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error: {ex.Message}");
        }
    }
}
```

## Running the Application
* Place your image (e.g., test-image.png) inside the project folder.
* Ensure the tessdata folder contains the required .traineddata file.
* Compile and run the project.

# Web API
```
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using System.IO;
using Tesseract;

[Route("api/ocr")]
[ApiController]
public class OcrController : ControllerBase
{
    private readonly string _tessDataPath = "./tessdata"; // Adjust path accordingly

    [HttpPost("extract-text")]
    public IActionResult ExtractText(IFormFile imageFile)
    {
        if (imageFile == null || imageFile.Length == 0)
            return BadRequest("No file uploaded.");

        try
        {
            using (var stream = imageFile.OpenReadStream())
            {
                using (var engine = new TesseractEngine(_tessDataPath, "eng", EngineMode.Default))
                {
                    using (var img = Pix.LoadFromStream(stream))
                    {
                        using (var page = engine.Process(img))
                        {
                            return Ok(new { Text = page.GetText().Trim() });
                        }
                    }
                }
            }
        }
        catch (Exception ex)
        {
            return StatusCode(500, $"Error: {ex.Message}");
        }
    }
}
```
## Testing
```
curl -X POST "http://localhost:5000/api/ocr/extract-text" -F "imageFile=@test-image.png"
```

# Conclusion
* This approach allows you to convert images to text using C# with Tesseract OCR. You can integrate this solution into desktop apps, web APIs, or background services.
* Would you like help with deployment or additional features like multi-language support? 🚀
