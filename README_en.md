
# Yolanda Bluetooth SDK Android Version

You can use most of yolanda company's scale after integration of this SDK successfully

If you need IOS version, please click [here](https://github.com/YolandaQingniu/qn-ble-sdk-ios) file

Android Studio project, please build new project and copy the related source files into it if you need use Eclipse

## The newest version `3.11.2` [please download here](https://github.com/YolandaQingniu/qn-ble-sdk-android/releases/download/3.11.2/qn-ble-sdk-android-3.11.2.zip)

Support set weight unit(kg,lb,jin)

The Demo project can tell you how to set weight unit.


```java

/**
 * kg,default value
 */
int WEIGHT_UNIT_KG = 0;

/**
 * lb
 */
int WEIGHT_UNIT_LB = 1;

/**
 * jin, Chinese weight unit,half of kg
 */
int WEIGHT_UNIT_JIN = 2;

/**
 * set weight unit ,include all value which use weight unit,like weight
 *
 * <p>
 * Scale will show kg on some old device does not support . <strong> jin </strong>
 * </p>
 *
 * @param unit ref {@link #WEIGHT_UNIT_KG} ,{@link #WEIGHT_UNIT_LB} ,{@link #WEIGHT_UNIT_JIN}
 */
void setWeightUnit(int unit);

/**
 *
 * @return {@link QNBleApi#WEIGHT_UNIT_KG} ,{@link QNBleApi#WEIGHT_UNIT_LB} ,{@link QNBleApi#WEIGHT_UNIT_JIN}
 */
int getWeightUnit();

/**
 * convert kg value to appointed unit
 *
 * @param weightUnit
 * @return value of appointed unit {@link QNBleApi#WEIGHT_UNIT_KG} ,{@link QNBleApi#WEIGHT_UNIT_LB} ,{@link QNBleApi#WEIGHT_UNIT_JIN}
 */
float convertUnit(int weightUnit, float kgWeight);

```

[All version](https://github.com/YolandaQingniu/qn-ble-sdk-android/releases)

## Integration manual

1. After importing the "Demo" into the "AS", please copy the latest SDK jar package and so file on to the lib directory
  * The jar package file is named qn-ble-api-x.x.jar
  * The so file is named libyolanda_calc.so, SDK provides 8 kinds of CPU architecture so library, you can choose according to your own project situation

2. Please Initialize the Yolanda SDK in Application
```java
/**
 * Please only call "onCreate" for just one time in Application under Internet work when Initialize the Yolanda SDK
 *
 * @param  AppId appId assigned by Yolanda
 * @param  callback ： Callback execution results, yolanda will try to ensure that all cases will be a callback
 */
 QNApiManager.getApi(getApplicationContext()).initSDK("123456789", new QNResultCallback() {
      @Override
      public void onCompete(int errorCode) {
          //Callback results,0 means success, otherwise please check all kinds of error codes in the api documentation
      }
});
```

3. Please Turn on Bluetooth scanning when call with startLeScan
```java
/**
 * @param deviceName Bluetooth device name, it will only scan the specified Bluetooth name of the device if it is not empty, otherwise there is no limit
 * @param mac        Bluetooth device mac address, it will only scan the specified Bluetooth mac address of the device if it is not empty, otherwise there is no limit
 * @param callback   Scan the callback port of the Bluetooth device
 **/
QNApiManager.getApi(this).startLeScan(null,null,new new QNBleScanCallback() {
  //It will return the error code if it fails
  public void onCompete(int errorCode) {e
  }
  //it will recall to this function if SDK scan the BLE device
  public void onScan(QNBleDevice bleDevice) {
  }
});
```

4. Connected with the scan callback device QNBleDevice

```java
/**
 * Connect to the specified device, all the data or connection status will be callback in QNBleCall. except on the onComplete method it will callback in the main thread
 * @param bleDevice Scan the callback interface Bluetooth devices
 * @param userId    User identification, unique user, non-empty string, you can use the user name, phone number, mailbox or other identification
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
     * @param status  Yolanda Bluetooth status
     */
    void onDisconnected(QNBleDevice bleDevice,int status);

    /**
     * Received an unstable weight data, it will continue to be called during user starting weighing, and will callback in the main thread
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
     * Received the stored data, callback in the main thread
     *
     * @param bleDevice Yolanda Bluetooth device
     * @param datas     The stored data array(it contains multiple array),you can use {@link QNData#getUserId()} to distinguish different user's data
     */
    void onReceivedStoreData(QNBleDevice bleDevice, List<QNData> datas);

    /**
     * The battery power of the terminal battery is low, and the connection is only called once
     */
    void onLowPower();
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

## proguard-rules

-keep public class com.kitnew.ble.QNCalc {*;}



## please note:

* After Use **testing version APPID** debugging successfully, please use **formal APPID** make on line
* **Formal APPID** is distributed by yolanda company

===================================================

If you have anything question please refer related documents carefully, if you have any questions about API please read our API user manual first

If still can not solve this problem please contact us for technical support, we will arrange SDK technical team to support you, email: huangdunren@yolanda.hk thank you !
