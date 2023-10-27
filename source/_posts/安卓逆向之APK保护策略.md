---
title: APK反破解技术
toc: true
date: 2022-09-29 16:55:42
tags:
categories:
- 安卓逆向
- 入门手册
---





# 



# JAVA代码混淆

不混淆 jdk-gui 打开的代码 就会一览无余 

重命名 变量名 方法名

# 资源混淆对抗

# 签名校验

签名就是程序里面给一个key 对key值进行操作，之后对key值进行检测 如果不一样 则签名无效，这就是签名校验

签名才能安卓安装否则黑屏 雷电模拟除外

我们这里以一个apk程序举例，拖入安卓killer

# 模拟器检测

```java
package com.qianyu.antiemulator;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;

import android.content.Context;
import android.telephony.TelephonyManager;
import android.util.Log;

public class AntiEmulator {
	
	// 检测/dev/socket/qemud和/dev/qemu_pipe这两个通道
	private static String[] known_pipes = { "/dev/socket/qemud",
			"/dev/qemu_pipe" };
	
	public static boolean checkPipes() {
		for (int i = 0; i < known_pipes.length; i++) {
			String pipes = known_pipes[i];
			File qemu_socket = new File(pipes);
			if (qemu_socket.exists()) {
				Log.v("Result:", "Find pipes!");
				return true;
			}
		}
		Log.i("Result:", "Not Find pipes!");
		return false;
	}
	
	
	// 检测驱动文件内容
	// 读取文件内容，然后检查已知QEMU的驱动程序的列表
	private static String[] known_qemu_drivers = { "goldfish" };

	public static Boolean checkQEmuDriverFile() {
		File driver_file = new File("/proc/tty/drivers");
		if (driver_file.exists() && driver_file.canRead()) {
			byte[] data = new byte[1024]; // (int)driver_file.length()
			try {
				InputStream inStream = new FileInputStream(driver_file);
				inStream.read(data);
				inStream.close();
			} catch (Exception e) {
				// TODO: handle exception
				e.printStackTrace();
			}
			String driver_data = new String(data);
			for (String known_qemu_driver : AntiEmulator.known_qemu_drivers) {
				if (driver_data.indexOf(known_qemu_driver) != -1) {
					Log.i("Result:", "Find know_qemu_drivers!");
					return true;
				}
			}
		}
		Log.i("Result:", "Not Find known_qemu_drivers!");
		return false;
	}
	
	// 检测模拟器上特有的几个文件
	private static String[] known_files = {
			"/system/lib/libc_malloc_debug_qemu.so", "/sys/qemu_trace",
			"/system/bin/qemu-props" };
	
	public static Boolean CheckEmulatorFiles() {
		for (int i = 0; i < known_files.length; i++) {
			String file_name = known_files[i];
			File qemu_file = new File(file_name);
			if (qemu_file.exists()) {
				Log.v("Result:", "Find Emulator Files!");
				return true;
			}
		}
		Log.v("Result:", "Not Find Emulator Files!");
		return false;
	}
	
	
	// 检查模拟器默认的电话号码
	private static String[] known_numbers = { "15555215554", "15555215556",
			"15555215558", "15555215560", "15555215562", "15555215564",
			"15555215566", "15555215568", "15555215570", "15555215572",
			"15555215574", "15555215576", "15555215578", "15555215580",
			"15555215582", "15555215584", };
	
	public static Boolean CheckPhoneNumber(Context context) {
		TelephonyManager telephonyManager = (TelephonyManager) context
				.getSystemService(Context.TELEPHONY_SERVICE);

		String phonenumber = telephonyManager.getLine1Number();

		for (String number : known_numbers) {
			if (number.equalsIgnoreCase(phonenumber)) {
				Log.v("Result:", "Find PhoneNumber!");
				return true;
			}
		}
		Log.v("Result:", "Not Find PhoneNumber!");
		return false;
	}
	
	
	// 检测设备ids是不是000000000000000
	private static String[] known_device_ids = { "000000000000000" };
	
	public static Boolean CheckDeviceIDS(Context context) {
		TelephonyManager telephonyManager = (TelephonyManager) context
				.getSystemService(Context.TELEPHONY_SERVICE);

		String device_ids = telephonyManager.getDeviceId();

		for (String know_deviceid : known_device_ids) {
			if (know_deviceid.equalsIgnoreCase(device_ids)) {
				Log.v("Result:", "Find ids: 000000000000000!");
				return true;
			}
		}
		Log.v("Result:", "Not Find ids: 000000000000000!");
		return false;
	}
	
	
	// 检测imsi id是不是310260000000000
	private static String[] known_imsi_ids = { "310260000000000" };

	public static Boolean CheckImsiIDS(Context context) {
		TelephonyManager telephonyManager = (TelephonyManager) context
				.getSystemService(Context.TELEPHONY_SERVICE);

		String imsi_ids = telephonyManager.getSubscriberId();

		for (String know_imsi : known_imsi_ids) {
			if (know_imsi.equalsIgnoreCase(imsi_ids)) {
				Log.v("Result:", "Find imsi ids: 310260000000000!");
				return true;
			}
		}
		Log.v("Result:", "Not Find imsi ids: 310260000000000!");
		return false;
	}

	
	// 检测手机上的一些硬件信息
	public static Boolean CheckEmulatorBuild(Context context) {
		String BOARD = android.os.Build.BOARD;
		String BOOTLOADER = android.os.Build.BOOTLOADER;
		String BRAND = android.os.Build.BRAND;
		String DEVICE = android.os.Build.DEVICE;
		String HARDWARE = android.os.Build.HARDWARE;
		String MODEL = android.os.Build.MODEL;
		String PRODUCT = android.os.Build.PRODUCT;
		if (BOARD == "unknown" || BOOTLOADER == "unknown" || BRAND == "generic"
				|| DEVICE == "generic" || MODEL == "sdk" || PRODUCT == "sdk"
				|| HARDWARE == "goldfish") {
			Log.v("Result:", "Find Emulator by EmulatorBuild!");
			return true;
		}
		Log.v("Result:", "Not Find Emulator by EmulatorBuild!");
		return false;
	}

	
	// 检测手机运营商家
	public static boolean CheckOperatorNameAndroid(Context context) {
		String szOperatorName = ((TelephonyManager) context
				.getSystemService("phone")).getNetworkOperatorName();
		if (szOperatorName.toLowerCase().equals("android") == true) {
			Log.v("Result:", "Find Emulator by OperatorName!");
			return true;
		}
		Log.v("Result:", "Not Find Emulator by OperatorName!");
		return false;
	}
	
	// 基于模拟器cpu的信息检测
	//cpu信息检测主要是在cpu信息看看是否包含intel、amd等字段，很多模拟器目前对于cpu信息还无法进行模拟。
//模拟器的cpu和真机的cpu有所不同
//--般而言模拟器读取的是电脑上的cpu，电脑是Inter或者AMD，那么模拟器的cpu也是Inter或者AMD，而手机一般是ARM。
//要读取cpu信息，可以用java代码或者c代码，用c代码就要使用jni。

	public static String readCpuInfo() {
		String result = "";
		try {
			String[] args = { "/system/bin/cat", "/proc/cpuinfo" };
			ProcessBuilder cmd = new ProcessBuilder(args);

			Process process = cmd.start();
			StringBuffer sb = new StringBuffer();
			String readLine = "";
			BufferedReader responseReader = new BufferedReader(
					new InputStreamReader(process.getInputStream(), "utf-8"));
			while ((readLine = responseReader.readLine()) != null) {
				sb.append(readLine);
			}d
			responseReader.close();
			result = sb.toString().toLowerCase();
		} catch (IOException ex) {
		}
		return result;
	}
	
	private boolean checkCPUInfo() {
    	//String[] blockList = "google_sdk,sdk,sdk_x86,vbox86p".split(",");  
		String cpuInfo = readCpuInfo();  
        if ((cpuInfo.contains("intel") || cpuInfo.contains("amd"))) {
        	return true;
        } 
        return false;
	}
    
   //关键路径检测特定模拟器检测
	private static String[] known_bluestacks = {
			"/data/app/com.bluestacks.appmart-1.apk",
			"/data/app/com.bluestacks.BstCommandProcessor-1.apk",
			"/data/app/com.bluestacks.help-1.apk",
			"/data/app/com.bluestacks.home-1.apk",
			"/data/app/com.bluestacks.s2p-1.apk",
			"/data/app/com.bluestacks.searchapp-1.apk",
			"/data/bluestacks.prop", 
			"/data/data/com.androVM.vmconfig",
			"/data/data/com.bluestacks.accelerometerui",
			"/data/data/com.bluestacks.appfinder",
			"/data/data/com.bluestacks.appmart",
			"/data/data/com.bluestacks.appsettings",
			"/data/data/com.bluestacks.BstCommandProcessor",
			"/data/data/com.bluestacks.bstfolder",
			"/data/data/com.bluestacks.help", 
			"/data/data/com.bluestacks.home",
			"/data/data/com.bluestacks.s2p",
			"/data/data/com.bluestacks.searchapp",
			"/data/data/com.bluestacks.settings",
			"/data/data/com.bluestacks.setup",
			"/data/data/com.bluestacks.spotlight",
			"/mnt/prebundledapps/bluestacks.prop.orig" 
	};

	public static boolean checkBlueStacksFiles() {
		for (int i = 0; i < known_bluestacks.length; i++) {
			String file_name = known_bluestacks[i];
			File qemu_file = new File(file_name);
			if (qemu_file.exists()) {
				Log.e("Result:", "Find BlueStacks Files!");
				return true;
			}
		}
		Log.e("Result:", "Not Find BlueStacks Files!");
		return false;
    }  
}

```

