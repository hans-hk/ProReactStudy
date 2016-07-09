.babelrc
```javascript
{
  "presets": [
    "es2015-webpack"
    ,"react"
  ],
  "plugins": [
    "transform-runtime",
    ["transform-es2015-modules-commonjs-simple", {
      "noMangle": true
    }]
  ]
}
```

npm 설치
```javascript
npm install --save-dev babel-plugin-transform-runtime 
npm install --save-dev babel-preset-es2015-webpack
npm install --save-dev babel-plugin-transform-es2015-modules-commonjs-simple
```

webpack.config.js

```javascript
module: {
    loaders: [
        {
            loader: "babel-loader",
            include: [path.resolve(__dirname, "src")]                
        }
    ]
},
devtool: '#inline-source-map'
```