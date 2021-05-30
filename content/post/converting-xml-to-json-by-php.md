+++
title = "Converting Xml to Json by Php"
date = "2014-07-14"
slug = "2014/07/14/converting-xml-to-json-by-php"
categories = []
+++
{% blockquote Chris Maden %}
XML is like violence: if it doesn't solve your problem, you aren't using enough of it. 
{% endblockquote %}

This post aims to convert bulk of *xml* data in a folder to the *json*(Javascript String Object Notation) format. Through this way we can remove unwanted data or filter the data to a specific format, thus increasing the flexibility of our output data.
<!-- more --> PHP provides a few handy functions in doing this. I will first list down and explain these handy functions (as revision) and then start coding. 

 + simplexml_load_file()
 	 
This function converts XML document into an object.
```php
$xml = '<?xml version="1.0" encoding="UTF-8" ?>
			<country>
				<city>
					Alor Star
				</city>
			</country>';

$xml = simplexml_load_file($xml);
echo $xml->country->city;
// Alor Star
```
 + json_encode()
 	
`json_encode()` changes all input value into a JSON representation of value.
```
echo json_encode([a, 'string', 3, <tag>]);
//["a", "string", 3, "<tag>"]

```
Also, we can add options like *JSON_UNESCAPED_UNICODE* to make sure foreign character like _ひらがな_　doesn't break. Also *JSON_PRETTY_PRINT* to do as it said (print nicely).

 + glob()
  
`glob()` provides convenient way to match many files in a folder according to the pattern given.
```
foreach(glob("images/*.{png,jpg}", GLOB_BRACE)) {
	// do something with all png and jpg format images
}
```
The optional parameter `GLOB_BRACE` expands {png,jpg} to match *'png'* or *'jpg'*. We can omit it if we only have single pattern to match. For instance `glob("*.xml")` to match all xml extension files.

 + basename()
 	
This function returns the base name of given path.
```
echo basename("path/xml/first.xml", ".xml");
// first
```
This is useful when we want to rename our new `json` files similar from the `xml` files. For instance, we will want data in `first.xml` to be store in `first.json`.

 + fopen(), fwrite(), fclose()
 
Let's see how we can create new file and write new data into it.
```
// open a file 		
$handle = fopen('file.txt', 'w') or die("Failed to open file.");
// write data to file
fwrite($handle, 'Some new data');
// close the file
fclose($handle);
```
3 steps. Open the file, copy the data and close it. The second parameter of `fopen()` indicates the type of access. In this case *w* means open for writing only. If the file doesn't exist, it will create a new one. If we just want to read the file we can use *r*.

###Start Converting
Revision is done and we can start converting now. Let's us assume our folder structure as below:
```
index.php
--\xml
		first.xml
		two.xml
		three.xml
--\json
```
All of our existed xml files are stored in the *xml* folder. We will convert all of these files into the *json* format and place them in the *json* folder. In this blog, I will convert [xml data](https://github.com/yang-wei/xml2json/blob/master/xml/countries.xml) which contain information of all countries in the world. `index.php` might looks like this:

```
<?php 
header("Content-Type: application/json") // set the header content-types

function xml2json($file){
	// main function to do the convertion
}

$xmlfiles = glob('xml/*.xml')	// get ready for the xml files
foreach ($xmlfiles as $xmlfile){
	// loop through each file
	// create new file, copy data
}

?>
```
The `index.php` is mainly divided into 2 main parts - function and the loop. Let's write the code for the former.
```
function xml2json($file){
	if(file_exist($file)) {
		// if the file exist, convert them into object
		$xml = simplexml_load_file($file);
		$output = [];

		foreach ($xml->country as $key->$value) {
			$filtered_xml = [];
			// here is where we filter data etc. You can even do something
			// like convert epoch time to human readable time here.
			$filtered_xml["name"] = $value["name"];
			$filtered_xml["currency"] = $value["currency"];

			// convert them into pretty JSON string
			$json = json_encode(($filter_xml), JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE);
			array_push($output, $json);
		}

		//separate all objects by comma 
		return "[" . implode(",", $output) ."]";

	} else {
			exit("Failed to open". $file);
	}
}
```
Here to make it simpler, we only take 2 data from `xml` which is *name* and *currency*. Next let's code the 2nd part. 
```
// loop through directory for each xml file
$xmlfiles = glob('xml/*.xml');

foreach ($xmlfiles as $xmlfile) {
    //set the output file name to .json
    $filename = basename($xmlfile, ".xml").".json";
    $result = xml2json($xmlfile);
    //create new file

    $handle = fopen($_SERVER['DOCUMENT_ROOT'] ."/xml2json/json/". $filename, "w") or die ('Cannot open file: ' .$filename);

    //write json data into new file
    if( fwrite($handle, $result) === FALSE) {
        echo "Cannot write to file.";
        exit;
    }

    echo "Success wrote date to file:" .$filename ."\n";
    fclose($handle);
    }

```
Nothing difficult here, just step by step. First we store all the files to convert in the `$xmlfiles` variable. Next we loop through each of them, create new file and write all the data which is *return* from our `xml2json()` function. Note that `$_SERVER['DOCYMENT_ROOT']` returns current environment path, in this case `http://localhost:`. Finally we close the file using the function `fclose()`.

This is the first part of my mini project. Each week I will try to update a mini project(mostly PHP) to keep myself learning. That's all for this post. The result of the output data can be viewed [here](https://github.com/yang-wei/xml2json). 

###Resource
 + [PHP manual](http://php.net/)
