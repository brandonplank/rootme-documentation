# rootme-documentaion
Based off of Jake James rootme, but up to date for iOS 11 - 13.x

That being said, some documentation has changed!

How to run Xcode apps as root and unsandboxed while still debugging them easiliy?

Many new jailbreak devs are find it difficult and/or a hassle to run apps as root and debug them at the same time. I got this idea when working on a project, a cydia application (eta **not** son) which needed root, but at the same time was complex and I needed a good and easy way to debug it. That's why I made this tool. At first it was private, considering any App Store apps can bundle this method (if they're smart enough to bypass the App Store checks) but I just reminded a project of MasBog where he could detect if an app was being debugged or not, I implemented that and made rootme only work while the app is being debugged. Huge thanks to MasBog!

# How to set it up?

- Install rootme from my repo (https://repo.brandonplank.org/) and run it, preferably using SSH on a computer or NewTerm 2. (This way you can terminate it when you're done using Control + C). 
- Grab LightMessaging headers and add them into your include path (https://github.com/rpetrich/LightMessaging)
- Grab rocketbootstrap headers (https://github.com/rpetrich/RocketBootstrap/tree/master)
- Take any xcode project and link it with librocketbootstrap.dylib (Take this from your device, it's on /usr/lib))
- Include "rocketbootstrap.h" and "LightMessaging.h" on your main.m file
- Add this code on it:
    ```
    LMConnection connection = {
        MACH_PORT_NULL,
        "org.brandonplank.rootme"
    };
    LMResponseBuffer buffer;
    NSString *pid = [NSString stringWithFormat:@"%d", getpid()];
    const char *msg = [pid UTF8String];
    SInt32 messageId = 0x1111;
    printf("UID before: %d\n", getuid());
    LMConnectionSendTwoWay(&connection, messageId, msg, strlen(msg) + 1, &buffer);
    LMMessage *response = &(buffer.message);
    const char *data = LMMessageGetData(response);
    printf("Finished? %s\n", data);
    LMResponseBufferFree(&buffer);
    printf("should have been rooted by now\n");
    printf("uid after: %d\n", getuid());
    setgid(0);
    printf("gid after: %d\n", getgid());
    ```
- Done! Enjoy!

Notes:
- rootme will give your app root permissions on uid and gid
- rootme will fully unsandbox your app
- killing an app using the stop button on Xcode doesn't seem to work but you can kill it from the app switcher yourself
- if you find any bugs tell me
- Will not open-source because it'll be easy for people to manipulate it. 
- Credits to: Jake James, 0x7ff.
