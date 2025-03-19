# Image to Text Converter in C#
* [ChatGPT](https://chatgpt.com/)
* [Tessdata](https://github.com/tesseract-ocr/tessdata)
* .NET (C#)

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
