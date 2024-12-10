# Webpack

## Introduction
Webpack is a static module bundler for modern JavaScript applications. It builds a dependency graph of your project's modules and generates one or more bundles optimized for production.

## Basic Configuration

### Entry and Output
```javascript
const path = require('path');

module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].[contenthash].js',
        clean: true
    },
    mode: 'production',
    target: 'web'
};
```

### Module Rules
```javascript
module.exports = {
    module: {
        rules: [
            // JavaScript/JSX processing
            {
                test: /\.(js|jsx)$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['@babel/preset-env', '@babel/preset-react']
                    }
                }
            },
            // CSS processing
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            // Asset handling
            {
                test: /\.(png|svg|jpg|jpeg|gif)$/i,
                type: 'asset/resource'
            }
        ]
    }
};
```

## Advanced Features

### Code Splitting
```javascript
module.exports = {
    entry: {
        main: './src/index.js',
        vendor: './src/vendor.js'
    },
    optimization: {
        splitChunks: {
            chunks: 'all',
            minSize: 20000,
            minRemainingSize: 0,
            minChunks: 1,
            maxAsyncRequests: 30,
            maxInitialRequests: 30,
            enforceSizeThreshold: 50000,
            cacheGroups: {
                defaultVendors: {
                    test: /[\\/]node_modules[\\/]/,
                    priority: -10,
                    reuseExistingChunk: true
                },
                default: {
                    minChunks: 2,
                    priority: -20,
                    reuseExistingChunk: true
                }
            }
        }
    }
};
```

### Development Server
```javascript
module.exports = {
    devServer: {
        static: {
            directory: path.join(__dirname, 'public')
        },
        compress: true,
        port: 3000,
        hot: true,
        historyApiFallback: true,
        proxy: {
            '/api': {
                target: 'http://localhost:8080',
                secure: false,
                changeOrigin: true
            }
        }
    }
};
```

## Plugin Configuration

### Essential Plugins
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');

module.exports = {
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html',
            filename: 'index.html',
            minify: {
                collapseWhitespace: true,
                removeComments: true
            }
        }),
        new MiniCssExtractPlugin({
            filename: '[name].[contenthash].css'
        })
    ],
    optimization: {
        minimizer: [
            `...`,
            new CssMinimizerPlugin()
        ]
    }
};
```

## Performance Optimization

### Bundle Analysis
```javascript
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
    plugins: [
        new BundleAnalyzerPlugin({
            analyzerMode: 'static',
            reportFilename: 'bundle-report.html',
            openAnalyzer: false
        })
    ]
};
```

### Caching Strategy
```javascript
module.exports = {
    output: {
        filename: '[name].[contenthash].js',
        chunkFilename: '[name].[contenthash].chunk.js'
    },
    optimization: {
        moduleIds: 'deterministic',
        runtimeChunk: 'single',
        splitChunks: {
            cacheGroups: {
                vendor: {
                    test: /[\\/]node_modules[\\/]/,
                    name: 'vendors',
                    chunks: 'all'
                }
            }
        }
    }
};
```

## Environment Configuration

### Environment Variables
```javascript
const webpack = require('webpack');
const dotenv = require('dotenv');

module.exports = () => {
    const env = dotenv.config().parsed;
    
    const envKeys = Object.keys(env).reduce((prev, next) => {
        prev[`process.env.${next}`] = JSON.stringify(env[next]);
        return prev;
    }, {});

    return {
        plugins: [
            new webpack.DefinePlugin(envKeys)
        ]
    };
};
```

### Production vs Development
```javascript
module.exports = (env) => {
    return {
        mode: env.production ? 'production' : 'development',
        devtool: env.production ? 'source-map' : 'eval-source-map',
        optimization: {
            minimize: env.production,
            minimizer: env.production ? [
                new TerserPlugin({
                    terserOptions: {
                        compress: {
                            drop_console: true
                        }
                    }
                })
            ] : []
        }
    };
};
```

## Build Scripts

### Package.json Scripts
```json
{
    "scripts": {
        "start": "webpack serve --mode development",
        "build": "webpack --mode production",
        "build:analyze": "webpack --mode production --analyze",
        "build:dev": "webpack --mode development",
        "watch": "webpack --watch"
    }
}
```

## Conclusion
Webpack provides powerful bundling and optimization capabilities for modern web applications, with extensive configuration options for different development needs.
