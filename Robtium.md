# Robtium RC 环境搭建 #
------------------
##  所需工具  ##

### JDK ###

推荐使用jdk1.6或者以上的[版本](http://www.oracle.com/technetwork/java/javase/downloads/index.html)。

### Android SDK ###

推荐使用从Android官网下下来的[整合工具](http://developer.android.com/sdk/index.html)，其整合了Android最新的sdk和eclipse，让你不必为配置环境而烦恼。

### Apache Ant ###

[版本](http://ant.apache.org/bindownload.cgi)必须为1.8或以上

### Robtium RC jar包与实例 ###

[Robtium RC jar包](http://sourceforge.net/projects/safsdev/files/Robotium%20RemoteControl/)

必要的包括：

1. robotium-messages.jar
2. robotium-serializable.jar
3. safsautoandroid.jar
4. safs-remotecontrol.jar
5. safssockets.jar

这些包都可以在x:\robotiumrc\SoloRemoteControl\libs路径下找到

ddmlib.jar
这个包则可以在%ANDROID_HOME%\tools\lib里面找到

-  Android-apktool

[apktool](http://android-apktool.googlecode.com/files/apktool1.5.2.tar.bz2)(用于解析apk文件并获取res文件夹)

akptool的使用方法请参照[此处](https://code.google.com/p/android-apktool/w/list)

-  re-signer


[re-signer](http://www.troido.de/download/re-sign.jar)用于去除并重签名apk文件，还可通过此软件获取***程序主Activity与程序包名***

## 环境配置 ##

### Jdk配置 ###

打开控制面板|系统和安全|系统|高级系统设置|环境变量


新建变量(用户变量，系统变量均可)JAVA_HOME
变量名：JAVA_HOME；变量值：C:\Program Files\Java\jdk1.6.0_43（***变量值需参考实际路径***）

编辑变量PATH（如不存在则新建，存在则添加）
变量名：PATH；变量值：.;%JAVA_HOME%\bin; %JAVA_HOME%\jre\bin;

编辑变量CLASSPATH（如不存在则新建，存在则添加）
变量名：CLASSPATH；变量值：.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\toos.jar;

点击确定存储变量并打开cmd命令提示符并键入”java -version”，返回类似如下提示则JDK配置成功

-  Android SDK配置

新建变量(用户变量，系统变量均可)ANDROID_HOME
变量名：ANDROID_HOME；变量值：X:\XXXX\Android\android-sdk（***变量值需参考实际路径***）

编辑变量PATH（如不存在则新建，存在则添加）
变量名：PATH；变量值：%ANDROID_HOME%\tools;%ANDROID_HOME%\platform-tools;%ANDROID_HOME%\build-tools\17.0.0; （***变量值需参考实际路径***）

### Ant配置 ###


新建变量(用户变量，系统变量均可)ANT_HOME
变量名：ANT_HOME；变量值：x:\xxx\apache-ant-1.9.2\bin(***变量值需参考实际路径***）
把该变量添加到环境变量里的path里面去

-  解压robtium rc相关的包到一个路径下(推荐解压到某个盘的根目录下)，解压后的路径为x:\robotiumrc

-  进入控制台，切换到刚才解压的目录里执行以下命令：

    Windows: Setup-Win.bat %ANDROID_HOME% or <path-to-android-sdk> 

    Unix: Setup-Unx.sh $ANDROID_HOME or <path-to-android-sdk> 

    Mac: Setup-Mac.sh $ANDROID_HOME or <path-to-android-sdk> 

### 重新build SAFSTCPMessenger-debug.apk： ###

为了与要使用的设备联系起来，需要重新设置SAFSTCPMessenger的设置；进入SAFSTCPMessenger文件夹(C:\robotiumrc\SAFSTCPMessenger)，打开local.properties，查看并修改以下属性：

sdk.dir=F:\\Development Tools\\adt-bundle-windows-x86_64-20130917\\sdk
safs.droid.automation.libs=C:\\robotiumrc\\RobotiumTestRunner\\libs

然后打开控制台切换到SAFSTCPMessenger目录下输入ant debug来重新build SAFSTCPMessenger-debug.apk，这一步完成后，打开C:\robotiumrc\SAFSTCPMessenger\bin查看是否存在SAFSTCPMessenger-debug.apk。

### 重新build SAFSTestRunner-debug.apk: ###

为了运行被测程序，需要对SAFSTestRunner-debug.apk进行一些设置，这些更改只需要一次；进入RobotiumTestRunner的文件夹(C:\robotiumrc\SAFSTestRunner)，打开local.properties，查看或修改一下属性:

sdk.dir=F:\\Development Tools\\adt-bundle-windows-x86_64-20130917\\sdk

然后编辑SAFSTestRunner的AndroidManifest.xml，修改<instrumentation内的值，把android:targetPackage设置为你要测试的程序:

android:targetPackage ="com.my.company.app.package"

然后打开控制台切换到SAFSTestRunner目录下输入ant debug来重新build SAFSTestRunner-debug.apk，这一步完成后，打开C:\robotiumrc\SAFSTestRunner\bin查看是否存在SAFSTestRunner-debug.apk。

### 实际上以上两步都可以在Eclipse当中快速完成: ###

在Eclipse当中点击打开->引入，在弹出窗口![pop](http://ntu.me/di/HV9G1/pop-up.jpg)选择Existing Android Code Into Workspace，点击“下一步”进入第二个窗口![pop2](http://ntu.me/di/22P37/pop-up2.jpg)，在root directory选中你的robtium rc的目录，然后再勾选上SAFSTCPMessenger和SAFSTestRunner，将他们导入到Eclipse里面。导入后，分别去修改SAFSTCPMessenger的local.properties当中的sdk.dir和safs.droid.automation.libs的值为![下图](http://ntu.me/di/A5XFG/1.jpg)，将SAFSTestRunner的AndroidManifest.xml当中的*instrumentation*标签修改为![下图](http://ntu.me/di/9SSFI/2.jpg) 

修改完成以后，就可以将两个包导出为apk了：![导出](http://ntu.me/di/LZOQO/1.jpg)

这样就完成了大部分的准备工作了。

# 使用ant完成apk重签名，反编译，生成R.java等工作 #
------------------------------------------

    <?xml version="1.0" encoding="UTF-8"?>
    <project basedir="." default="test" name="xx"><!--name就是你当前项目的名称-->
    	<property environment="env" />
    	<property name="base.dir" value="." />
    	<property name="testng.output.dir" value="${base.dir}/test-output" />
    	<property name="3rd.lib.dir" value="${base.dir}/libs" />
    	<property name="testng.file" value="testng.xml" />
    	<property name="apkResigner" value="${base.dir}/tools/re-sign.jar" />
    	<property name="apks.dir" value="${base.dir}/apks" />
    	<property name="testedAPK" value="${apks.dir}/Sportacular.apk" />
    	<property name="TestRunnerAPK" value="${apks.dir}/SAFSTestRunner.apk" />
    	<property name="SAFSTCPMessengerAPK" value="${apks.dir}/SAFSTCPMessenger.apk" />
    	<!-- property for generate R.java file -->
    	<property name="sdk-build-tools" value="${env.android_home}/build-tools/18.1.1" />
    	<property name="resource-dir" value="res" />
    	<property name="sdk-folder" value="${env.android_home}" />
    	<property name="sdk-platform-folder" value="${sdk-folder}/platforms/android-18" />
    	<property name="android-jar" value="${sdk-platform-folder}/android.jar" />
    	<property name="aapt" value="${sdk-build-tools}/aapt" />
    	<property name="adb" value="${sdk-folder}/platform-tools/adb" />
    	<property name="manifest-xml" value="AndroidManifest.xml" />
    
    	<taskdef resource="testngtasks" classpath="${3rd.lib.dir}/testng.jar" />
    
    	<target name="init">
    		<delete dir="${base.dir}/bin" />
    		<mkdir dir="${base.dir}/bin" />
    		<delete dir="${base.dir}/res" />
    		<mkdir dir="${base.dir}/res" />
    	</target>
    
    	<target name="resignApk" depends="init">
    		<java jar="${apkResigner}" fork="true">
    			<arg value="${testedAPK}" />
    			<arg value="${apks.dir}/android_mail_debug.apk" />
    		</java>
    		<java jar="${apkResigner}" fork="true">
    			<arg value="${TestRunnerAPK}" />
    			<arg value="${apks.dir}/SAFSTestRunner_debug.apk" />
    		</java>
    		<java jar="${apkResigner}" fork="true">
    			<arg value="${SAFSTCPMessengerAPK}" />
    			<arg value="${apks.dir}/SAFSTCPMessenger_debug.apk" />
    		</java>
    	</target>
    
    	<target name="decode-Res" depends="resignApk">
    		<java jar="${base.dir}/tools/apktool.jar" fork="true">
    			<arg value="d" />
    			<arg value="-f" />
    			<arg value="${apks.dir}/android_mail_debug.apk" />
    			<arg value="${base.dir}/temp" />
    		</java>
    	</target>
    
    	<target name="clean" depends="decode-Res">
    		<copydir src="${base.dir}/temp/res" dest="${base.dir}/res" />
    		<copy file="${base.dir}/temp/${manifest-xml}" todir="${base.dir}/"
    			overwrite="true" />
    		<delete dir="${base.dir}/temp" />
    	</target>
    
    	<target name="gen-R" depends="clean">
    		<echo>Generating R.java from the resources...</echo>
    		<exec executable="${aapt}" failonerror="true">
    			<arg value="package" />
    			<arg value="-f" />
    			<arg value="-m" />
    			<arg value="-J" />
    			<arg value="${base.dir}/src" />
    			<arg value="-S" />
    			<arg value="${resource-dir}" />
    			<arg value="-I" />
    			<arg value="${android-jar}" />
    			<arg value="-M" />
    			<arg value="${manifest-xml}" />
    		</exec>
    	</target>
    
    	<target name="native2ascii" depends="gen-R">
    		<native2ascii encoding="UTF-8" src="${base.dir}/src"
    			dest="${base.dir}/src" includes="**/*.java" ext=".java" />
    	</target>
    
    	<target name="compile" depends="native2ascii">
    		<javac encoding="UTF-8" srcdir="${base.dir}/src" destdir="${base.dir}/bin"
    			classpathref="classes" />
    	</target>
    
    	<path id="classes">
    		<fileset dir="${3rd.lib.dir}" includes="*jar" />
    		<pathelement location="${base.dir}/bin" />
    	</path>
    
    	<target name="test" depends="compile">
    		<testng classpathref="classes" outputdir="${testng.output.dir}"
    			haltOnfailure="false" delegateCommandSystemProperties="true">
    			<xmlfileset dir="${base.dir}" includes="${testng.file}" />
    		</testng>
    	</target>
    
    </project>