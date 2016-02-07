RoboRIO and Eclipse 2016 OpenCV Libraries (with Java)
===================

Attached is a compiled version of the OpenCV 2.4.10 libraries, generously made public by Team 2168: 
-->https://github.com/Team2168/2168_Vision_Example

I was able to get these libraries on the RoboRIO and running vision processing natively. It ended up requiring very little CPU usage and became a much better alternative to off board processors (which have power related issues).

Here is the details of the systems I used when I tested (Last tested 2/6/16)
-> Eclipse Mars 4.5.1
-> OpenCV 2.4.1
-> Installed through Macbook Pro running OSX El Capitan
-> FRC 2016 Season: Stronghold
-> Java JRE SE 1.8

### How to Install OpenCV on the RoboRIO

**Overview:** We are going to transfer over the libraries using rsync (on Mac or Linux), and then set up the environment varibles to work with the libraries.

1. Transfer to the lib_OpenCV folder inside of _Libraries/softfp/ to the Roborio's /usr/local/lib directory (such that you have /usr/local/lib/lib_OpenCV/ on the RoboRio).
2. FOR MAC/LINUX: Once the roboRIO is connected to Internet, install rsync by first ssh'ing in : `ssh admin@roboRIO-[TEAM_NUMBER]-FRC.local` and if it connects, it will prompt for a password (for the admin account, or whatever account you choose to SSH into). Then, verify the connection by running `ping www.google.com` from the roboRIO shell. You should get a Reply. Next, run `opkg update` followed by `opkg install rsync`
3. Exit the roboRIO shell and run (in your terminal on Linux or Mac) `rsync -a path/to/_Libraries/softfp/lib_OpenCV admin@roboRIO-[TEAM_NUMBER]-FRC.local:/usr/local/lib/`
4. Now SSH back into the Roborio with the same command as specified earlier
5. In the RoboRio terminal type `vi /etc/ld.so.conf.d/opencv.conf`, a new file will open inside the terminal window
6. Hit the `i` key to enter insert mode
7. Paste or type this as the only line in the file `/usr/local/lib/lib_OpenCV`
8. Hit `esc` to exit insert mode
9. Type `:wq` and then hit enter to save and exit the file
10. You should now be back at the terminal prompt
11. Run `ldconfig` in the rio terminal
12. you may get some lines that say xxx file is not a symlink, thats ok
13. done

>If using windows you can use WinSCP, if using linux or putty use rsync instead of cp command because the files contain symlinks. You will have to install rsync on the Rio first using opkg update, opkg install rsync, while connected to the internet. TO CONNECT TO THE INTERNET (this is how we did it, one of many ways)
- Connect the RoboRIO to a Windows computer using an Ethernet cable
- Go to Network settings and turn on Wifi Sharing
- Now the RoboRIO should have Internet!

### To use in Java with Eclipse
1. Create a new Java Project
2. Copy the _Libraries folder to your development machine
3. add _Libraries/softFP/lib_OpenCV/java/opencv-2410.jar to your java project build path by `right clicking on project > Configure Build Path >  Add External Jar` and then select the appropriate location of the jar.
4. import org.opencv.core.Core; and any other libraries (look up opencv Java documentation for help)
5. Open up the build.xml, and to the last line before </project> write, `<var name="classpath" value="${classpath}:/path/to/_Libraries/softFP/lib_OpenCV/java/opencv-2410.jar"/>`. This basically tells the builder where to look when compiling. Consider using ${user.home} if you are using multiple computers, and then uniformly installing everyone's folder in the same place (for example, *value="${classpath}:${user.home}/git/RoborioOpenCV2016/_Libraries/softFP/lib_OpenCV/java/opencv-2410.jar*). If you are transferring to a new computer, make sure to verify this every time. Save and close build.xml.
6. Now, any place you want to run OpenCV code in your program, type in Eclipse: `System.load("/usr/local/lib/lib_OpenCV/java/libopencv_java2410.so");` so that the roboRIO knows where it is supposed to pull the library from.
7. You should be good to go!

### Pulling from a Video Stream
We used an IP Axis Camera 206, but most of this should work with any IP camera. Verify that the stream works on your web browser if you are running into errors.

Your code should look something like this in robotInit():

```java

VideoCapture vcap;
public void robotInit() {
	System.load("/usr/local/lib/lib\_OpenCV/java/libopencv\_java2410.so");
	try{
		vcap = new VideoCapture("http://[USERNAME]:[PASSWORD]@[IP_ADDRESS]/axis-cgi/mjpg/video.cgi user=[USERNAME]&password=[PASSWORD]&channel=0&.mjpg");
	}
	catch (Exception e){
		System.out.println("ERROR WITH CAM");
	}
}

```

Of course, make sure to fill in USERNAME, PASSWORD, and IP\_ADDRESS as appropriate. The goal here is to create a VideoCapture object that pulls from the IP camera. Verify that this is correct by loading everything up until the `user=` part in your web browser. For example, http://[USERNAME]:[PASSWORD]@[IP\_ADDRESS]/axis-cgi/mjpg/video.cgi would show us a live stream of the camera. 

>Note: Include a port in the address like this: [USERNAME]:[PASSWORD]@[IP\_ADDRESS]:[PORT] if there is one.



Good Luck and please report any issues!

- Team 649 (2016)
