# Image to Text Converter in C#
* [ChatGPT](https://chatgpt.com/)
* [Tessdata](https://github.com/tesseract-ocr/tessdata)
* .NET (C#)

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
