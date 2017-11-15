# RTC:PCL Grabber for Microsoft Kinect

大阪電気通信大学  
升谷 保博  
2017年3月29日

## はじめに

- Microsoft Kinectから深度と色の情報を読み取り，RTC:PCLのPointCloud型（`PointCloudTypes::PointCoud`）を
出力するRTコンポーネントです．
- 以下の環境で開発，動作確認しています．
  - Windows 10 64bit版
  - Visual Studio 2012
  - OpenRTM-aist 1.1.2 64bit版
  - Point Cloud Library 1.7.2 AllinOne (VS2012 64bit用)
  - Microsoft Kinect XBOX 360
  - Kinect for Windows SDK v1.8
- 杉浦 司 (UnaNancyOwen) 氏の
[Grabber of Point Cloud Library based on Kinect for Windows SDK ](https://github.com/UnaNancyOwen/KinectGrabber/tree/KinectGrabber)のコードを
そのまま利用しています（`KinectGrabber\kinect_grabber.h`）．
- CMakeでKinect SDKを見つけるモジュールも杉浦 司氏の
[`FindKinectSDK.cmake`](https://github.com/UnaNancyOwen/KinectGrabber/blob/KinectGrabber/Sample/FindKinectSDK.cmake)
をそのまま使っています．
- `pointcloud.idl` は，Geoffrey Biggs (gbiggs)氏の
[RT-Components for the Point Cloud Library](https://github.com/gbiggs/rtcpcl/)
に[含まれているもの](https://github.com/gbiggs/rtcpcl/blob/master/pc_type/pointcloud.idl)
をそのまま使っています．

## 仕様

- height: 480
- width: 640
- type: "xyzrgb"
- is_bigendian: 0
- point_step: 16
- row_step: 10240
- is_dense: 0
- fields (name,offset,data_type,count)
  - [0]: "x",0,6,4
  - [1]: "y",4,6,4
  - [2]: "z",8,6,4
  - [3]: "b",12,1,1
  - [4]: "g",13,1,1
  - [5]: "r",14,1,1

## インストール

- [OpenRTM-aist 1.1.2](http://www.openrtm.org/openrtm/ja/node/6034)をインストール．
- [PCL-1.7.2のWindows用AllInOne](https://onedrive.live.com/?authkey=!ACFnPNzPhXJ0FtU&id=EC9EBB2646FF189A!49089&cid=EC9EBB2646FF189A)
をインストール（2017年3月下旬に削除されたようです）．
- [Kinect for Windows SDK v1.8](https://www.microsoft.com/en-us/download/details.aspx?id=40278)
をインストール．
- [RTCPCLKinect](https://github.com/MasutaniLab/RTCPCLKinect)
をクローンかダウンロードする．
- CMake
  - ビルドディレクトリはトップ直下の`build`
  - ConfigureはVisual Studio 64bit
- `build\RTCPCLKinect.sln`をVisual Studioで開く．
- パフォーマンスを出すために，Releaseでビルドがお勧め．

## 使い方

- KinectをUSBポートに接続する．
- 出力されるデータ量が多いので，CORBAのデフォルトの設定ではエラーになります．
rtc.confに`corba.args: -ORBgiopMaxMsgSize`の設定が必要です．
トップディレクトリのrtc.confでは`corba.args: -ORBgiopMaxMsgSize 20971520`
にしています（デフォルト値の10倍）．
- コンポーネントを起動するバッチファイル`RTCPCLKinectComp.bat`を用意しています．
  - ビルドディレクトリがトップ直下の`build`であることを仮定しています．
  - 環境変数`RTM_VC_CONFIG`を`Debug`か`Release`に設定してください．
- 動作確認のための接続相手として，
[PointCloudViewer](https://github.com/MasutaniLab/PointCloudViewer)
を使ってください．

## 既知の問題・TODO

- [`pointcloud.idl`](https://github.com/gbiggs/rtcpcl/blob/master/pc_type/pointcloud.idl)に記述されている仕様と，[RT-Components for the Point Cloud Library](https://github.com/gbiggs/rtcpcl/)の関数の仕様が異なっていますが，`pointcloud.idl`本来の仕様のデータを出力するようにしました（2017年4月2日）．
- 1回に送出するデータ量が約4.7MB．もっと圧縮して送る方がいいのかもしれません．
- Windowsでしか動作しません．