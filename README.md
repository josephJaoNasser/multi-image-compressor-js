# multi-output-image-compressor-js
Compress an image/s into multiple sizes all at once. This image compressor returns multiple versions of an image resized to "tiny", "small", "medium", "large", and "larger" variants. 

### Download the code to see how it works or if you want to modify the Sharp JS parameters or the code itself

## Dependencies

- Sharp JS must be installed before using this module.
```npm
npm i sharp
```
- __Use Multer memory storage to upload the image to memory to get the file buffer.__
```npm
npm i multer
```

## Usage

### __Compress Single__
```compressSingle()``` by default will return an array 4 resized versions of an image, namely tiny, small, medium, large  
```javascript
const imageCompressor = require('./image-compressor')

imageCompressor.compressSingle(file).then(compressedImages => {

   //do what you want with the compressed images
   
}).catch(err => {

   //handle your error here
   
})
```

### __Compress Multiple__
```compressMultiple()``` by default will return an array of 4 resized versions of EACH UPLOADED IMAGE, namely tiny, small, medium, large  
```javascript
const imageCompressor = require('./image-compressor')

imageCompressor.compressMultiple(files).then(compressedImages => {

   //do what you want with the compressed images
   
}).catch(err => {

   //handle your error here
   
})
```

### __Specifying the range of compression__
```compressSingle()``` and ```compressMultiple()``` has 2 extra parameters: ```smallestSize``` and ```largestSize```. By default, they are set to 0 and 4, which means the compressor will return the sizes from "tiny" up to the "larger" sizes, and will always output all 5 versions for each uploaded image. If yow want to select the range of compression, you must specify the ```smallestSize``` and ```largestSize```

The compression levels are as follows:
|Level|   Size    |
| ---      | ---       |
| 0 | Tiny |
| 1 | Small |
| 2 | Medium |
| 3 | Large |
| 4 | Larger |

#### Example 1:
In the code example below using ```compressSingle()```, the smallest size is set to 1 and largest size is set to 3, hence the function will ONLY return the small, medium, and large variants of the image
```javascript
const imageCompressor = require('./image-compressor')

//setting the range to 1 and 3 returns the small, medium, and large compressed images
imageCompressor.compressSingle(file, 1, 3).then(compressedImages => {

   //do what you want with the compressed images
   
}).catch(err => {

   //handle your error here
   
})
```
#### Example 2:
In this example using ```compressMultiple()```,  the smallest and largest sizes are set to 0 and 2 respectively. In this case the function will return the Tiny, Small, and Medium variants of EACH IMAGE.
```javascript
const imageCompressor = require('./image-compressor')

//setting the range to 0 and 2 returns the tiny, small, and medium versions for each image uploaded
imageCompressor.compressMultiple(files, 0, 2).then(compressedImages => {

   //do what you want with the compressed images
   
}).catch(err => {

   //handle your error here
   
})
```

### __Compress images to either Tiny, Small, Medium, Large, Larger__
If you want to compress an image/s to a specific level and return only one output for each image, you can call either of the following functions:
```javascript
const imageCompressor = require('./image-compressor')

// compress single tiny
imageCompressor.compressSingleTiny(file)

// compress single small
imageCompressor.compressSingleSmall(file)

// compress single medium
imageCompressor.compressSingleMedium(file)

// compress single large
imageCompressor.compressSingleLarge(file)

// compress single larger
imageCompressor.compressSingleLarger(file)

// OR 

// compress multiple tiny
imageCompressor.compressMultipleTiny(files)

// compress multiple small
imageCompressor.compressMultipleSmall(files)

// compress multiple medium
imageCompressor.compressMultipleMedium(files)

// compress multiple large
imageCompressor.compressMultipleLarge(files)

// compress multiple larger
imageCompressor.compressMultipleLarger(files)

```

### __When using with multer and express router:__

When using ```compressSingle()```:
```javascript
const imageCompressor = require('./image-compressor')
const multer = require('multer');

//upload to memory using multer
const memoryStorage = new multer.memoryStorage()
// assuming you appended 'single-image' to the formData that you sent to the server. Call this whatever you want before sending
const uploadToMemory = multer({storage:memoryStorage}).single('single-image') 

router.post('/upload', uploadToMemory, async(req, res)=>{ 
  imageCompressor.compressSingle(req.file).then(images => {
  
   //do whatever you want with the images
   
  }).catch(err => { 
  
   //handle your error
   
  }
})

```

When using ```compressMultiple()```
```javascript
const imageCompressor = require('./image-compressor')
const multer = require('multer');

//upload to memory using multer
const memoryStorage = new multer.memoryStorage()
// assuming you appended 'multiple-images' to the formData that you sent to the server. Call this whatever you want before sending
// you can set the '4' to as many as you want
const uploadToMemory = multer({storage:memoryStorage}).array('multiple-images', 4)

router.post('/upload', uploadToMemory, async(req, res)=>{ 
  imageCompressor.compressSingle(req.files).then(images => {
  
   //do whatever you want with the images
   
  }).catch(err => { 
  
   //handle your error
   
  }
})

```


## __Additional Information__
SharpJS uses the ```.resize()``` function to specify the width/height and the ```.jpeg({quality: n })``` to specify the quality of the compressed image. The functions inside this JS file only specify the width when calling ```.resize()```. The height will automatically adjust based on the width.

The width and quality for each variant are as follows:
|Level|   Size (width) | Quality |
| ---  | ---  | ---  |
| Tiny | 60 | 70 |
| Small | 150 | 70 |
| Medium | 320 | 80 |
| Large | 800 | 90 |
| Larger | 1500 | 90 |

If the images width is smaller than any of these sizes, the image will still be resized. For example, if an image's size is only 800px and is ran through the ```compressSingleLarger()``` function, then the image will be enlarged up to 1500px. 

