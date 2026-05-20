# OCR
## Tesseract
**Install**
```
sudo nala install tesseract-ocr
```
**Check the available language packs**
```
apt-cache search tesseract-ocr-
```
**Install greek language pack**
```
sudo nala install tesseract-ocr-ell
```
**List installed language packs**
```
tesseract --list-langs
```
**Usage**
```
tesseract 01.pdf output -l ell
```
## OCRmyPDF
**Install**
```
sudo nala install ocrmypdf
```
**Usage**
```
ocrmypdf 01.pdf output.pdf
```