# React Native PoC

- Setup Environment (React Native CLI)
    - install brew
    - install node (f not already installed), watchman and cocoapods
    
    ```bash
    brew install node
    brew install watchman
    brew install cocoapods
    ```
    
    - install xcode from app store
- Create new app
    - Use react native’s built in cli to init new app
    
    ```bash
    npx react-native init WakuRelay
    ## or with typescript
    npx react-native init WakuRelay --template react-native-template-typescript
    ```
    
    ```bash
    Run instructions for iOS:
        • cd "/Users/jem/code/react-native-waku-poc/WakuRelay" && npx react-native run-ios
        - or -
        • Open WakuRelay/ios/WakuRelay.xcworkspace in Xcode or run "xed -b ios"
        • Hit the Run button
    ```
    
    ```bash
    cd ios/
    pod install
    ```
    
- Run app
    - Start Metro (like webpack)
    
    ```bash
    npx react-native start
    ```
    
    - start the app (in a new terminal window)
    
    ```bash
    npx react-native run-ios
    ```
    
- Waku
    - install js-waku
    
    ```bash
    npm install --save js-waku
    ```
    
    - Will get a warning when starting app with metro
    
    ```bash
    warn Package js-waku has been ignored because it contains invalid configuration. Reason: Package subpath './package.json' is not defined by "exports" in /Users/jem/code/react-native-waku-poc/WakuRelay/node_modules/js-waku/package.json
    ```
    
    - Add waku code to App.tsx
    - Get error about `xxxxx` library
    
    ```bash
    npm install --save assert buffer crypto-browserify stream-browserify
    ```
    
    ```bash
    	- metro.config.js
    module.exports = {
      resolver: {
        extraNodeModules: {
          assert: require.resolve('assert'),
          buffer: require.resolve('buffer'),
          crypto: require.resolve('crypto-browserify'),
          stream: require.resolve('stream-browserify'),
        },
      },
    
    ```
    
    - heartbeat error, force it to use 0.13.0 (Fixed in ****Release 0.20.0****)
    
    ```bash
    npm install libp2p-gossipsub@0.13.0 --save
    
    ## Add to metro.config.js > resolver > extraNodeModules
    "libp2p-gossipsub": "^0.13.0",
    ```
    
    - issue with run-ios
    
    ```bash
    ## in ios/
    pod cache clean --all
    
    ## upgrade in package.json
    "react-native-tcp": "^4.0.0",
    "react-native-udp": "^4.0.0",
    
    npm i
    
    # ios/
    pod install
    ```
    
    **ERROR (ios)**
    
    ```bash
    79 duplicate symbols for architecture x86_64
    ```
    
    **FIX (seems like every time a `pod install` is done)**
    
    ```bash
    - Open project in xcode
    - Project Navigator > Pods project > Pods
    - Under targets, select TcpSockets
    - Build Phases > Compile sources
    - Delete reference to `CocoaAsyncSocket`
    ```
    
    **ERROR (metro)**
    
    ```bash
    ERROR  TypeError: Conversion from 'BigInt' to 'number' is not allowed.
    ```
    
    ```bash
    ## BigInt error
    
    ## https://github.com/facebook/react-native/issues/28492#issuecomment-824698934
    
    ## babel.config.js ??
    plugins: ['@babel/plugin-syntax-bigint'],
    
    npm i --save-dev rn-nodeify@latest
    npm install --save big-integer
    ./node_modules/.bin/rn-nodeify --hack --install
    ./node_modules/.bin/rn-nodeify --install "big-integer" --hack
    
    ## shim.js - just after the Buffer shim
    if (typeof BigInt === 'undefined') {
      global.BigInt = require('big-integer');
    }
    ```
