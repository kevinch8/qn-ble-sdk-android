
#Yoland Bluetooth SDK Android Version

You can use most of yolanda company's scale after integration of this SDK successfully

If you need IOS version,please click [here](../../../qn-ble-sdk-ios) file 

Android Studio project, please build new project and copy the related soruce files into it if you need use Eclipse

## The newest version `2.6` [please download here](../../releases/download/2.6/qn-ble-sdk-android-2.6.zip)
Increase advanced index   scores
Optimize performance

[All version](../../releases) 

##Integration manual

1. After importing the "Demo" into the "AS", please copy the latest SDK jar package and so file on to the lib directory
  * The jar package file is named qn-ble-api-x.x.jar
  * The so file is named libyolanda_calc.so, SDK provides 8 kinds of CPU architecture so library, you can choose according to your own project situation
  
2. Please Initialize the Yoland SDK in Application
```java
/**
 * Please only call "onCreate" for just one time in Application under Internet work when Initialize the Yoland SDK
 *
 * @param  AppId appId assigned by Yolanda
 * @param  isRelease ：Whether it is release mode, if yes please setting false, and setting to ture when on line
 * @param  callback ： Callback execution results, yolanda will try to ensure that all cases will be a callback
 */
 QNApiManager.getApi(getApplicationContext()).initSDK("123456789", false, new QNResultCallback() {
      @Override
      public void onCompete(int errorCode) {
          //Callback results,0 means success, otherwise please check all kinds of error codes in the api documentation
      }
});
```

3. Please Turn on Bluetooth scanning when call with startLeScan
```java
/**
 * @param deviceName Bluetooth device name, it will only scan the specified Bluetooth name of the device if it is not empty, otherwise there is no limiti
 * @param mac        Bluetooth device mac address,it will only scan the specified Bluetooth mac address of the device if it is not empty, otherwise there is no limiti
 * @param callback   Scan the callback port of the Bluetooth device
 **/
QNApiManager.getApi(this).startLeScan(null,null,new new QNBleScanCallback() { 
  //It will return the error code if it fails
  public void onCompete(int errorCode) { 
  }
  //It will return each device's information for just one time if it scaned the device
  public void onScan(QNBleDevice bleDevice) {
  }
});
```

4. Connected with the scaned callback device QNBleDevice

```java
/**
 * Connect to the specified device, all the data or connection status will be callback in QNBleCall. except on the onComplete method it will callback in the main thread
 * @param bleDevice Scan the callback interface Bluetooth devices
 * @param userId    User identification,unique user,non-empty string, you can use the user name, phone number, mailbox or other identification
 * @param height    Height in cm
 * @param gender    Gender:  Male: 1 Female: 0
 * @param birthday  Birthday, accurate to the day
 * @param callback  The callback port during weighing
 */
QNApiManager.getApi(this).connectDevice(device, "userId", 170, 1, birthday, new new QNBleCallback() {
    /**
     * start to connect, it will callback in the main thread
     *
     * @param bleDevice Yolanda Bluetooth device
     */
    void onConnectStart(QNBleDevice bleDevice);

    /**
     * connected ,it will callback in the main thread
     *
     * @param bleDevice 
	 Yolanda Bluetooth device
     */
    void onConnected(QNBleDevice bleDevice);

    /**
     * 
	 Disconnected from the Bluetooth connection,it will callback in the main thread
     *
     * @param bleDevice  Yolanda Bluetooth device
     */
    void onDisconnected(QNBleDevice bleDevice);

    /**
     * Received an unstable weight data, it will continue to be called during user starting weighing,and will callback in the main thread
     *
     * @param bleDevice  Yolanda Bluetooth device
     * @param weight    Unstable weight data
     */
    void onUnsteadyWeight(QNBleDevice bleDevice, float weight);

    /**
     * Received an stable weight data and it will callback in the main thread
     *
     * @param bleDevice Yolanda Bluetooth device
     * @param data      Yolanda weighing data
     */
    void onReceivedData(QNBleDevice bleDevice, QNData data);

    /**
     * Received the stored data,callback in the main thread
     *
     * @param bleDevice Yolanda Bluetooth device
     * @param datas     The stored data array(it contains multiple array),you can use {@link QNData#getUserId()} to distinguish different user's data
     */
    void onReceivedStoreData(QNBleDevice bleDevice, List<QNData> datas);
});
```

### onCompete：The return explanation of callback

```java
/**
  * execution succeed
 */
int QN_SUCCESS = 0;
/**
 * APPID is invalid
 */
int QN_UNAVAILABLE_APP_ID = 1;
/**
 * Network no open
 */
int QN_NETWORK_CLOSED = 2;
/**
 * network timeout
 */
int QN_NETWORK_TIMEOUT = 3;
/**
 * No low power Bluetooth (Bluetooth 4.0 and above)
 */
int QN_NO_BLE = 4;
/**
 * Bluetooth error
 */
int QN_BLE_ERROR = 5;
/**
 * Bluetooth version too low
 */
int QN_BLE_LOW_VERSION = 6;
/**
 * Bluetooth is not turned on
 */
int QN_BLE_CLOSED = 7;

/**
 * SDK version too low
 */
int QN_BLE_LOW_SDK_VERSION = 8;
```

## please note:

* Testing version APPID：123456789 , the testing ver server may be unstable during testing
* After Use ** testing version APPID **debugging successfully, please switch to ** release ** mode and use ** formal APPID ** make on line
* **Formal APPID ** is distributed by yolanda company
* you can choose a test or release mode in SDK

===================================================

If you have anyting question please refer related documents carefully, if you have any questions about API please read our API user manual first

If still can not solve this problem please contact us for technical support, we will arrange SDK technical team to support you, thank you !
