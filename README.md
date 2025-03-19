# Image to Text Converter in C#
* [ChatGPT](https://chatgpt.com/)
* [Tessdata](https://github.com/tesseract-ocr/tessdata)
* [ConsoleApp Template](https://github.com/gtechsltn/ConsoleApp)
* [Log4Net](https://github.com/gtechsltn/ConsoleApp/blob/master/ConsoleApp1/Src/App.config)
* [ConsoleApp in .NET (C#)](https://github.com/gtechsltn/ConsoleApp/blob/master/ConsoleApp1/Src/Program.cs)

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
