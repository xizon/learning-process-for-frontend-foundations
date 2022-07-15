# Node初级功能示例(01)


## 目录

 - [解压zip文件](#解压zip文件)
 - [保存文件夹未zip格式](#保存文件夹未zip格式)
 - [将图片转化为base64](#将图片转化为base64)
 - [生成一个新的html文件](#生成一个新的html文件)
 - [http请求库(got()的用法)](#http请求库got的用法)
 - [远程保存图片图像到本地](#远程保存图片图像到本地)
 - [移动文件并改名](#移动文件并改名)
 - [(批量遍历)删除,新建,读取文件夹或文件](#批量遍历删除新建读取文件夹或文件)
 - [爬取网页信息](#爬取网页信息)

## 解压zip文件
----
参考代码:

```js
/**
 * Unzip file
 */
 const extract = require('extract-zip')
 const path = require('path');
 const dabaBasePath = path.join(__dirname, '../public/assets/');
 
 /**
  * @param {String} sourceDir: /some/folder/to/compress.zip
  * @param {String} outPath: /path/to/
  * @returns {Promise}
  */
 function unzipFile(sourceDir, outPath) {
     return async () => {
         try {
             await extract(sourceDir, { dir: outPath })
             console.log('Extraction complete');
         } catch (err) {
             // handle any errors
         }
     }
 }
 

 unzipFile( path.join(__dirname, '../public/assets/F-Curator-html.zip'), dabaBasePath)();
```


## 保存文件夹未zip格式
----
参考代码:

```js
const path = require('path');
const fs = require('fs');
const archiver = require('archiver');
const dabaBasePath = path.join(__dirname, '../db/');

/**
 * @param {String} sourceDir: /some/folder/to/compress
 * @param {String} outPath: /path/to/created.zip
 * @returns {Promise}
 */
function zipDirectory(sourceDir, outPath) {
    const archive = archiver('zip', { zlib: { level: 9 } });
    const stream = fs.createWriteStream(outPath);

    return new Promise((resolve, reject) => {
        archive
            .directory(sourceDir, false)  // append files from a sub-directory, putting its contents at the root of archive
            .on('error', err => reject(err))
            .pipe(stream)
            ;

        stream.on('close', () => resolve());
        archive.finalize();
    });
}

// Dind the desktop path of macOS
const homeDir = require('os').homedir(); // See: https://www.npmjs.com/package/os
const desktopDir = `${homeDir}/Desktop`;
zipDirectory(dabaBasePath, desktopDir + '/target.zip');

```

## 将图片转化为base64
----
参考代码:

```js
const fs = require('fs');
const contents = fs.readFileSync('/path/to/file.jpg', {encoding: 'base64'});
```

或者：

```js
const fs = require('fs').promises;
const contents = await fs.readFile('/path/to/file.jpg', {encoding: 'base64'});
```

## 生成一个新的html文件
----
参考代码:

```js
 const path = require('path');
 const fs = require('fs');

 const fileName = path.join(__dirname, '../db/db.html');
 const stream = fs.createWriteStream(fileName);
 
 
 stream.once('open', function(fd) {
    const html = buildHtml();
 
   stream.end(html);
 });


 function buildHtml() {
   return `
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta http-equiv="X-UA-Compatible" content="IE=edge">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Document</title>
   </head>
   <body>
       
   </body>
   </html>
   `;
 };

```

## http请求库(got()的用法
----
参考代码:

```js
 const got = require('got');
 const jsdom = require("jsdom");
 const JSDOM = jsdom.JSDOM;


 got('https://www.npmjs.com', {
    timeout: {
        request: 5000
    }
}).then(response => {
    const dom = new JSDOM(response.body);
    console.log( dom );
}).catch(err => {
    console.log( err );
});
```

等同于：
```js
const crawl = async () => {
    const body = await got.get('https://www.npmjs.com', {
        timeout: {
            request: 5000
        }
    });
    return new JSDOM(body);
};

crawl();

```

## 远程保存图片图像到本地
----
参考代码:

```js
/**
 * Extract remote resources
 */

/**
 * Usage:
 * 
const { save } = require('./downloader');
const path = require('path');
const targetDir = path.resolve(__dirname, `../db/img`);
save( 'https://examples.png', targetDir, function( res ) {
    console.log(res);
})();
 * 
 */

const axios = require('axios');
const path = require('path');
const fs = require('fs');

const newDir = path.resolve(__dirname, `../db/img`);
if (!fs.existsSync(newDir)) {
    fs.mkdirSync(newDir);
}


function getBase64(url) {
    return axios
        .get(url, {
            responseType: 'arraybuffer'
        })
        .then(response => {
            return Buffer.from(response.data, 'binary').toString('base64');
        })
        .catch(error => {
            // return Promise.reject(error);
            if (error.response) {
                return 'e';
            }
            
        });
}




function save(url, targetDir, callback, name = '') {
	return async () => {
        const b64string = getBase64( url );
        let fileData = new Buffer.from(await b64string, 'base64');

        if ( fileData.toString() === '' ) {
            callback.call(null, null);
        } else {
            //console.log('fileData: ', fileData); // <Buffer ff d8 ff e0 00 . 15556 more bytes>
            
            const extension = path.extname(url);
            const randomStr = (Math.random() + 1).toString(36).substring(7);
            let fileName = (typeof(name) === 'undefined' || name === '' ) ? path.basename(url) : name;

            fileName = fileName.replace(`${extension}`, `-${randomStr}${extension}`);
            fs.writeFileSync(`${targetDir}/${fileName}`, fileData);

            callback.call(null, fileName);
        }

	}
}

function move(oldPath, newPath, callback) {
    fs.rename(oldPath, newPath, function (err) {
        if (err) {
            if (err.code === 'EXDEV') {
                copy();
            } else {
                console.log("err", err);

                callback(err);
            }
            return;
        }
        callback();
    });
    function copy() {
        var readStream = fs.createReadStream(oldPath);
        var writeStream = fs.createWriteStream(newPath);
        readStream.on('error', callback);
        writeStream.on('error', callback);

        readStream.on('close', function () {
            let fileName = path.basename(newPath)
            console.log(fileName, "  file uploaded")
            //remove path from destination
            //fs.unlink(oldPath, callback);
            // do your stuff
        });
        readStream.pipe(writeStream);
    }
}

module.exports = {
    move,
    save
};
```

## 移动文件并改名
----
参考代码:

```js
const path = require('path');
const fs = require('fs');

const newDir = path.resolve(__dirname, `../db/img`);
if (!fs.existsSync(newDir)) {
    fs.mkdirSync(newDir);
}


function move(oldPath, newPath, callback) {
    fs.rename(oldPath, newPath, function (err) {
        if (err) {
            if (err.code === 'EXDEV') {
                copy();
            } else {
                console.log("err", err);

                callback(err);
            }
            return;
        }
        callback();
    });
    function copy() {
        var readStream = fs.createReadStream(oldPath);
        var writeStream = fs.createWriteStream(newPath);
        readStream.on('error', callback);
        writeStream.on('error', callback);

        readStream.on('close', function () {
            let fileName = path.basename(newPath)
            console.log(fileName, "  file uploaded")
            //remove path from destination
            //fs.unlink(oldPath, callback);
            // do your stuff
        });
        readStream.pipe(writeStream);
    }
}

move(`${newDir}/icon.png`, `${newDir}/1.png`, function (res) {
    console.log(res);
});
```

## (批量遍历)删除,新建,读取文件夹或文件
----
参考代码:

```js
'use strict';

const path = require('path');
const glob = require('glob');
const fs = require('fs');


const packagesRoot = glob.sync( path.resolve(__dirname, '../packages/*/*.ts') );
const packagesSub = glob.sync( path.resolve(__dirname, '../packages/*.ts') );
const packages = packagesRoot.concat( packagesSub );
const componentsEntry = {};
packages.map( ( path ) => {
	const filename = path.split( '/' ).pop().replace('.ts', '');
	componentsEntry[ filename ] = path;
});


/*! 
 *************************************
 *  Clear npm files
 *************************************
 */

//Move the components to folders of root directory
const comNames = Object.keys( componentsEntry );
packages.map( ( comPath, index ) => {
    
    const newDir = path.resolve(__dirname, `../${comNames[index]}`);
    if (!fs.existsSync(newDir)){
        fs.mkdirSync(newDir);
    }

    //JS files
    const oldPathJS = path.resolve(__dirname, `../dist/cjs/${comNames[index]}.js`);
    const newPathJS = `${newDir}/index.js`;
    fs.rename(oldPathJS, newPathJS, function (err) {
        //if (err) throw err
        console.log(`Successfully ./${comNames[index]}/index.js`);
    });

});

//remove old folder
// Where the recursive option deletes the entire directory recursively.
fs.rmdirSync(path.resolve(__dirname, '../dist/cjs'), { recursive: true });

```

## 爬取网页信息
----
参考代码:

```js
const fs = require('fs');
const got = require('got');
const jsdom = require("jsdom");
const JSDOM = jsdom.JSDOM;

const vgmUrl = 'https://uiux.cc';

got(vgmUrl).then(response => {
	const dom = new JSDOM(response.body);
	
	console.log('=> title: ', dom.window.document.querySelector('title').textContent);
	console.log('=> icon: ', dom.window.document.head.querySelectorAll('link[rel~="icon"]')[0].href);

}).catch(err => {
	console.log(err);
});


```



	