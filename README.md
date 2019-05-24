
# 轻牛 蓝牙SDK 安卓版

[Document in English](https://github.com/YolandaQingniu/qn-ble-sdk-android/blob/master/README_en.md)

集成该 SDK,可以使用 伊欧乐公司旗下几乎所有的智能人体秤

如需使用 IOS 版,请点击 [这里](https://github.com/YolandaQingniu/qn-ble-sdk-ios)

Android Studio 工程，如需要使用Eclipse 请自行新建工程，并拷贝相关源文件到工程

## 最新版本 `3.11.3` [下载地址](https://github.com/YolandaQingniu/qn-ble-sdk-android/releases/download/3.11.3/qn-ble-sdk-android-3.11.3.zip)

* 增加更多设备支持

### 近期更新
* 增加测量过程中修改用户资料的方法
* 修改启动扫描逻辑，任何情况下调用开启扫描都会开始扫描设备
* 增加更多设备支持

[所有版本](https://github.com/YolandaQingniu/qn-ble-sdk-android/releases)

## 集成方法

1. 把Demo导入到 AS 后，请拷贝最新的SDK jar包和so文到lib目录
  * jar包文件名为 qn-ble-api-x.x.jar
  * so文件名为 libyolanda_calc.so,SDK 提供8种CPU架构的so库，可根据自己的项目情况选择

2. 在 Application种初始化轻牛的SDK
```java
/**
 * 初始化轻牛SDK,仅在Application中的 onCreate中调用，保证每次app实例都只调用一次。调用这个方法时，尽量要联网
 *
 * @param  AppId 由轻牛所分配的 appId
 * @param  callback 执行结果的回调,轻牛会尽量保证各种情况都会进行回调
 */
 QNApiManager.getApi(getApplicationContext()).initSDK("123456789", new QNResultCallback() {
      @Override
      public void onCompete(int errorCode) {
          //执行结果，为0则成功，其它则参考api文档的种的错误码
      }
});
```

3. 调用 startLeScan 启动蓝牙扫描
```java
/**
 * @param deviceName 蓝牙设备的蓝牙名，如果不为空则扫描只扫描指定蓝牙名的设备，为空则不限定
 * @param mac        蓝牙设备的mac地址，如果不为空则扫描只扫描指定mac地址的设备，为空则不限定
 * @param callback   扫描到蓝牙设备后回调的接口
 **/
QNApiManager.getApi(this).startLeScan(null,null,new new QNBleScanCallback() {
  //如果失败，会在这个方法中返回错误码
  public void onCompete(int errorCode) {
  }
  //如果扫描到设备，会在这个方法返回这个设备的相关信息
  public void onScan(QNBleDevice bleDevice) {
  }
});
```

4. 连接扫描到的回调设备QNBleDevice
```java
/**
 * 连接指定的设备，所有的数据或连接状态都会在QNBleCall种进行回调。除了onComplete方法外，，其它的都会在主线程进行回调
 *
 * @param bleDevice 扫描回调接口中的蓝牙设备
 * @param userId    用户标识，用户唯一，传非空的字符串，可以使用 用户名，手机号，邮箱等其它标识
 * @param height    身高，单位cm
 * @param gender    性别 男：1 女：0
 * @param birthday  生日，精确到天
 * @param callback  称重过程的回调接口
 */
QNApiManager.getApi(this).connectDevice(device, "userId", 170, 1, birthday, new new QNBleCallback() {
    /**
     * 开始连接 在主线程中回调
     *
     * @param bleDevice 轻牛蓝牙设备
     */
    void onConnectStart(QNBleDevice bleDevice);

    /**
     * 已经连接上了 在主线程中回调
     *
     * @param bleDevice 轻牛蓝牙设备
     */
    void onConnected(QNBleDevice bleDevice);

    /**
     * 断开了蓝牙连接 在主线程中回调
     *
     * @param bleDevice 轻牛蓝牙设备
     * @param status 蓝牙连接状态
     */
    void onDisconnected(QNBleDevice bleDevice,int status);

    /**
     * 收到了不稳定的体重数据，在称重前期会不断被调用 在主线程中回调
     *
     * @param bleDevice 轻牛蓝牙设备
     * @param weight    不稳定的体重
     */
    void onUnsteadyWeight(QNBleDevice bleDevice, float weight);

    /**
     * 收到了稳定的测量数据 在主线程中回调
     *
     * @param bleDevice 轻牛蓝牙设备
     * @param data      轻牛测量数据
     */
    void onReceivedData(QNBleDevice bleDevice, QNData data);

    /**
     * 收到了存储数据 在主线程中回调
     *
     * @param bleDevice 轻牛蓝牙设备
     * @param datas     存储数据数组（包含多个），可用{@link QNData#getUserId()}判断是哪个用户的数据
     */
    void onReceivedStoreData(QNBleDevice bleDevice, List<QNData> datas);

    /**
     * 称端电池电量过低时回调，每次连接只调用一次
     *
     */
    void onLowPower();
});
```

### onCompete方法的返回值说明

```java
/**
 * 执行成功
 */
int QN_SUCCESS = 0;
/**
 * APPID失效
 */
int QN_UNAVAILABLE_APP_ID = 1;
/**
 * 网络没开
 */
int QN_NETWORK_CLOSED = 2;
/**
 * 网络超时
 */
int QN_NETWORK_TIMEOUT = 3;
/**
 * 没有底功耗蓝牙(蓝牙4.0及以上)
 */
int QN_NO_BLE = 4;
/**
 * 蓝牙错误
 */
int QN_BLE_ERROR = 5;
/**
 * 蓝牙版本太低
 */
int QN_BLE_LOW_VERSION = 6;
/**
 * 蓝牙未开启
 */
int QN_BLE_CLOSED = 7;

/**
 * SDK的版本过低
 */
int QN_BLE_LOW_SDK_VERSION = 8;
```

### 混淆配置

打正式包时，在混淆配置文件中，加上以下配置

-keep public class com.kitnew.ble.QNCalc {*;}

## 注意事项

* 使用 **测试版APPID** 调试成功后，请使用 **正式的APPID** 上线
* **正式的APPID** 由轻牛公司统一分配

===================================================

如有使用问题,请先查阅文档,如果对 API 有疑问,请先查阅 **api** 目录中的 API使用文档

如果还是无法解决,请 email: huangdunren@yolanda.hk 或者直接在QQ，微信的SDK技术讨论组中咨询
