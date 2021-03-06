# SGKBarcodeBundle

[![Build Status](https://travis-ci.org/shangguokan/SGKBarcodeBundle.svg)](https://travis-ci.org/shangguokan/SGKBarcodeBundle)
[![Latest Stable Version](https://poser.pugx.org/sgk/barcode-bundle/v/stable)](https://packagist.org/packages/sgk/barcode-bundle) [![Total Downloads](https://poser.pugx.org/sgk/barcode-bundle/downloads)](https://packagist.org/packages/sgk/barcode-bundle) [![Latest Unstable Version](https://poser.pugx.org/sgk/barcode-bundle/v/unstable)](https://packagist.org/packages/sgk/barcode-bundle) [![License](https://poser.pugx.org/sgk/barcode-bundle/license)](https://packagist.org/packages/sgk/barcode-bundle)

SGKBarcodeBundle is the Symfony2 Barcode Generator Bundle what you want!
This README is also available in French ([Français](Resources/doc/README_fr.md)) and Chinese ([中文](Resources/doc/README_zh-CN.md)).

Features:

1. Support 3 two-dimensional (2D) and 30 one-dimensional (1D) Barcode types
2. Three output formats: HTML, PNG and SVG canvas
3. Twig integration: you can simply use a extensional function of Twig in the template to generate Barcode
4. Core of this bundle use this laravel project: [dinesh/barcode](https://github.com/dineshrabara/barcode)

![SGKBarcodeBundle](Resources/doc/README.png)

## Installation

Add SGKBarcodeBundle by running the command:
```sh
$ php composer.phar require sgk/barcode-bundle:dev-master
```

Or, add SGKBarcodeBundle to your ``composer.json``, then execute ``php composer.phar update``
```json
"require": {
        "sgk/barcode-bundle": "dev-master"
    }
```

Composer will install the bundle to your project's vendor/sgk directory.

Then, Enable the bundle in the kernel:
```php
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
        // ...
        new SGK\BarcodeBundle\SGKBarcodeBundle(),
    );
}
```

## Generate options

To generate one barcode, you have 5 options can be configured.

|option|type       |required|allowed values|description          |
|:----:|:---------:|:------:|:------------:|:-------------------:|
|code  |string     |required|              |what you want encoded|
|type  |string     |required|[Supported Types](#supported-barcode-types)|type of barcode|
|format|string     |required|html, svg, png|output format|
|width |**integer**|optional|              |**width of unit**|
|height|**integer**|optional|              |**height of unit**|
|color |string for html, svg / array for png|optional|[HTML Color Names](http://www.w3schools.com/html/html_colornames.asp) / array(R, G, B)|barcode color|

> Default width and height for 2D barcode are 5, 5, for 1D are 2, 30.
> Default color for html, svg is black, for png is array(0, 0, 0)

## Usage by service
  
The bundle registers one service: ``sgk_barcode.generator`` which will allows you to generate barcode:

* outpout html
```php
$options = array(
    'code'   => 'string to encode',
    'type'   => 'c128',
    'format' => 'html',
);

$barcode =
    $this->get('sgk_barcode.generator')->generate($options);
    
return new Response($barcode);
```

* outpout svg
```php
$options = array(
    'code'   => 'string to encode',
    'type'   => 'qrcode',
    'format' => 'svg',
    'width'  => 10,
    'height' => 10,
    'color'  => 'green',
);

$barcode =
    $this->get('sgk_barcode.generator')->generate($options);
    
return new Response($barcode);
```

* outpout png
```php
$options = array(
    'code'   => 'string to encode',
    'type'   => 'datamatrix',
    'format' => 'png',
    'width'  => 10,
    'height' => 10,
    'color'  => array(127, 127, 127),
);

$barcode =
    $this->get('sgk_barcode.generator')->generate($options);

return new Response('<img src="data:image/png;base64,'.$barcode.'" />');
```
> For format png, the generator return the based64 of png file, so you can get the real data of png by ``base64_decode($barcode)``. Here we use [Data URI scheme](http://en.wikipedia.org/wiki/Data_URI_scheme) to direct display the png in webpage.

## Usage in Twig template

This bundle extend one function of Twig: ``barcode`` which you can simply use it to generate barcode in the twig template.

``barcode`` use the same options, only different thing is your need pass a [Twig array](http://twig.sensiolabs.org/doc/templates.html#literals) (it looks really like Json, but it isn't) in the function.

* display html
```twig
{{ barcode({code: 'string to encode', type: 'c128', format: 'html'}) }}
```

* display svg
```twig
{{ barcode({code: 'string to encode', type: 'qrcode', format: 'svg', width: 10, height: 10, color: 'green'}) }}
```

* display png
```twig
<img src="data:image/png;base64,
{{ barcode({code: 'string to encode', type: 'datamatrix', format: 'png', width: 10, height: 10, color: [127, 127, 127]}) }}
" />
```

## Usage without service

```php
use SGK\BarcodeBundle\Generator\Generator;
//...
$options = array(
    'code'   => 'string to encode',
    'type'   => 'qrcode',
    'format' => 'html',
);

$generator = new Generator();
$barcode = $generator->generate($options);

return new Response($barcode);
```

## Save Barcode in file

As you can see, the Bundle save nothing on the file system, But if you want to keep the barcode, No problem!

* save as html
```php
$savePath = '/tmp/';
$fileName = 'sample.html';

file_put_contents($savePath.$fileName, $barcode);
```

* save as svg
```php
$savePath = '/tmp/';
$fileName = 'sample.svg';

file_put_contents($savePath.$fileName, $barcode);
```

* save as png
```php
$savePath = '/tmp/';
$fileName = 'sample.png';

file_put_contents($savePath.$fileName, base64_decode($barcode));
```

## Supported Barcode Types

Please read [Wikipedia page](http://en.wikipedia.org/wiki/Barcode) to know which type you should choice. 

### 2d barcodes

|type      |Name                                                   |Example(encode 123456)|
|:--------:|:-----------------------------------------------------:|:--------------------:|
|qrcode    |[QR code](http://en.wikipedia.org/wiki/QR_code)        |![](Resources/doc/barcode/qrcode.png)|
|pdf417    |[PDF417](http://en.wikipedia.org/wiki/PDF417)          |![](Resources/doc/barcode/pdf417.png)|
|datamatrix|[Data Matrix](http://en.wikipedia.org/wiki/Data_Matrix)|![](Resources/doc/barcode/datamatrix.png)|

### 1d barcodes

|type    |Symbology                                              |Example(encode 123456)|
|:------:|:-----------------------------------------------------:|:--------------------:|
|c39     |[Code 39](http://en.wikipedia.org/wiki/Code_39)        |![](Resources/doc/barcode/c39.png)|
|c39+    |Code 39 CHECK_DIGIT                                    |![](Resources/doc/barcode/c39+.png)|
|c39e    |Code 39 EXTENDED                                       |![](Resources/doc/barcode/c39e.png)|
|c39e+   |Code 39 EXTENDED CHECK_DIGIT                           |![](Resources/doc/barcode/c39e+.png)|
|c93     |[Code 93](http://en.wikipedia.org/wiki/Code_93)        |![](Resources/doc/barcode/c93.png)|
|s25     |[Standard 2 of 5](http://www.barcodeisland.com/2of5.phtml)           |![](Resources/doc/barcode/s25.png)|
|s25+    |Standard 2 of 5 CHECK_DIGIT                                          |![](Resources/doc/barcode/s25+.png)|
|i25     |[Interleaved 2 of 5](http://en.wikipedia.org/wiki/Interleaved_2_of_5)|![](Resources/doc/barcode/i25.png)|
|i25+    |Interleaved 2 of 5 CHECK_DIGIT                                       |![](Resources/doc/barcode/i25+.png)|
|c128    |[Code 128](http://en.wikipedia.org/wiki/Code_128)                    |![](Resources/doc/barcode/c128.png)|
|c128a   |Code 128A|![](Resources/doc/barcode/c128a.png)|
|c128b   |Code 128B|![](Resources/doc/barcode/c128b.png)|
|c128c   |Code 128C|![](Resources/doc/barcode/c128c.png)|
|ean2    |[EAN 2](http://en.wikipedia.org/wiki/EAN_2)                 |![](Resources/doc/barcode/ean2.png)|
|ean5    |[EAN 5](http://en.wikipedia.org/wiki/EAN_5)                 |![](Resources/doc/barcode/ean5.png)|
|ean8    |[EAN 8](http://en.wikipedia.org/wiki/EAN-8)                 |![](Resources/doc/barcode/ean8.png)|
|ean13   |[EAN 13](http://en.wikipedia.org/wiki/EAN-13)               |![](Resources/doc/barcode/ean13.png)|
|upca    |[UPC-A](http://en.wikipedia.org/wiki/Universal_Product_Code)|![](Resources/doc/barcode/upca.png)|
|upce    |[UPC-B](http://en.wikipedia.org/wiki/Universal_Product_Code)|![](Resources/doc/barcode/upce.png)|
|msi     |[MSI](http://en.wikipedia.org/wiki/MSI_Barcode)             |![](Resources/doc/barcode/msi.png)|
|msi+    |MSI CHECK_DIGIT                                             |![](Resources/doc/barcode/msi+.png)|
|postnet |[POSTNET](http://en.wikipedia.org/wiki/POSTNET)             |![](Resources/doc/barcode/postnet.png)|
|planet  |[PLANET](http://en.wikipedia.org/wiki/Postal_Alpha_Numeric_Encoding_Technique)|![](Resources/doc/barcode/planet.png)|
|rms4cc|[RMS4CC](http://en.wikipedia.org/wiki/RM4SCC)    |![](Resources/doc/barcode/rms4cc.png)|
|kix     |[KIX-code](http://nl.wikipedia.org/wiki/KIX-code)|![](Resources/doc/barcode/kix.png)|
|imb     |[IM barcode](http://en.wikipedia.org/wiki/Intelligent_Mail_barcode)|![](Resources/doc/barcode/imb.png)|
|codabar |[Codabar](http://en.wikipedia.org/wiki/Codabar)                    |![](Resources/doc/barcode/codabar.png)|
|code11  |[Code 11](http://en.wikipedia.org/wiki/Code_11)                    |![](Resources/doc/barcode/code11.png)|
|pharma  |[Pharmacode](http://en.wikipedia.org/wiki/Pharmacode)              |![](Resources/doc/barcode/pharma.png)|
|pharma2t|Pharmacode Two-Track                                               |![](Resources/doc/barcode/pharma2t.png)|

## Requirements

If there is some problem of requirements, make sure you have install these two extensions of PHP (check in your phpinfo()).

- Barcodes requires [GD](http://php.net/manual/en/book.image.php) and [ImageMagick](http://php.net/manual/en/book.imagick.php) to create PNGs in PHP 5.3.
- Barcodes requires [PHP bcmath](http://php.net/manual/en/book.bc.php) extension for Intelligent Mail barcodes

## Tests

To execute unit tests:
```sh
$ phpunit --coverage-text
```
