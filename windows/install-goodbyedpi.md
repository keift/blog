## 1. Uninstall all antivirus programs

Antivirus programs can corrupt GoodbyeDPI and make it unusable. If you have previously downloaded GoodbyeDPI while your antivirus program was running, you will need to delete it and download it again.

## 2. Download GoodbyeDPI

Press **Win + R** and type `cmd`.

```shell
# Go to the documents directory
cd %userprofile%/Documents

# Download the compiled zip file from GitHub
curl -Lo goodbyedpi-v1.1.0.zip https://github.com/fir4tozden/goodbyedpi/archive/refs/tags/v1.1.0.zip
```

## 3. Unzip the zip file

Extract the zip file and then delete it.

```shell
# Unzip the zip file
powershell -Command "Expand-Archive -Path './goodbyedpi-v1.1.0.zip' -DestinationPath './goodbyedpi-v1.1.0'"

# Delete the zip file that we no longer need
del goodbyedpi-v1.1.0.zip
```

## 4. Install GoodbyeDPI

Once everything is complete, we can start installing GoodbyeDPI.

```shell
# Enter the folder
cd ./goodbyedpi-v1.1.0/goodbyedpi-1.1.0

# Run install.bat
install.bat
```

🎉 That's it! You have now overcome all access barriers. Long live freedom!

## TIP: Uninstall GoodbyeDPI

If you ever regain your freedom, you can undo all of these actions in the following way.

```shell
# Go to the GoodbyeDPI directory
cd %userprofile%/Documents/goodbyedpi-v1.1.0/goodbyedip-1.1.0

# Run uninstall.bat
uninstall.bat
```
