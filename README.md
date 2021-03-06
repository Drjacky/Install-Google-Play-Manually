# Install Google Play Manually
How to install google play manually on any android devices

# You can read this article on [Medium](https://medium.com/@drjacky/how-to-install-google-play-store-manually-on-aosp-285a18c406db) too.

Add below lines (just betweens ByMe! comments) after about lines 5634, in PackageManagerService.java at
```javascript
/android/sourcecode/frameworks/base/services/java/com/android/server/pm:
```

```javascript
private boolean grantSignaturePermission(String perm, PackageParser.Package pkg,
                                          BasePermission bp, HashSet<String> origPermissions) {
        boolean allowed;
        allowed = (compareSignatures(
                bp.packageSetting.signatures.mSignatures, pkg.mSignatures)
                        == PackageManager.SIGNATURE_MATCH)
                || (compareSignatures(mPlatformPackage.mSignatures, pkg.mSignatures)
                        == PackageManager.SIGNATURE_MATCH);
        if (!allowed && (bp.protectionLevel
                & PermissionInfo.PROTECTION_FLAG_SYSTEM) != 0) {
            if (isSystemApp(pkg)) {
                // For updated system applications, a system permission
                // is granted only if it had been defined by the original application.
                if (isUpdatedSystemApp(pkg)) {
                    final PackageSetting sysPs = mSettings
                            .getDisabledSystemPkgLPr(pkg.packageName);
                    final GrantedPermissions origGp = sysPs.sharedUser != null
                            ? sysPs.sharedUser : sysPs;

                    if (origGp.grantedPermissions.contains(perm)) {
                        // If the original was granted this permission, we take
                        // that grant decision as read and propagate it to the
                        // update.
                        allowed = true;
                    } else {
                        // The system apk may have been updated with an older
                        // version of the one on the data partition, but which
                        // granted a new system permission that it didn't have
                        // before.  In this case we do want to allow the app to
                        // now get the new permission if the ancestral apk is
                        // privileged to get it.
                        if (sysPs.pkg != null && sysPs.isPrivileged()) {
                            for (int j=0;
                                    j<sysPs.pkg.requestedPermissions.size(); j++) {
                                if (perm.equals(
                                        sysPs.pkg.requestedPermissions.get(j))) {
                                    allowed = true;
                                    break;
                                }
                            }
                        }
                    }
                } else {
                    allowed = isPrivilegedApp(pkg);
                }
            }
        }
//LINE 5634 >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
// ByMe!
	if (pkg.packageName.equals("com.android.vending") && compareSignatures(pkg.mSignatures, new Signature[]{new Signature("308204433082032ba003020102020900c2e08746644a308d300d06092a864886f70d01010405003074310b3009060355040613025553311330110603550408130a43616c69666f726e6961311630140603550407130d4d6f756e7461696e205669657731143012060355040a130b476f6f676c6520496e632e3110300e060355040b1307416e64726f69643110300e06035504031307416e64726f6964301e170d3038303832313233313333345a170d3336303130373233313333345a3074310b3009060355040613025553311330110603550408130a43616c69666f726e6961311630140603550407130d4d6f756e7461696e205669657731143012060355040a130b476f6f676c6520496e632e3110300e060355040b1307416e64726f69643110300e06035504031307416e64726f696430820120300d06092a864886f70d01010105000382010d00308201080282010100ab562e00d83ba208ae0a966f124e29da11f2ab56d08f58e2cca91303e9b754d372f640a71b1dcb130967624e4656a7776a92193db2e5bfb724a91e77188b0e6a47a43b33d9609b77183145ccdf7b2e586674c9e1565b1f4c6a5955bff251a63dabf9c55c27222252e875e4f8154a645f897168c0b1bfc612eabf785769bb34aa7984dc7e2ea2764cae8307d8c17154d7ee5f64a51a44a602c249054157dc02cd5f5c0e55fbef8519fbe327f0b1511692c5a06f19d18385f5c4dbc2d6b93f68cc2979c70e18ab93866b3bd5db8999552a0e3b4c99df58fb918bedc182ba35e003c1b4b10dd244a8ee24fffd333872ab5221985edab0fc0d0b145b6aa192858e79020103a381d93081d6301d0603551d0e04160414c77d8cc2211756259a7fd382df6be398e4d786a53081a60603551d2304819e30819b8014c77d8cc2211756259a7fd382df6be398e4d786a5a178a4763074310b3009060355040613025553311330110603550408130a43616c69666f726e6961311630140603550407130d4d6f756e7461696e205669657731143012060355040a130b476f6f676c6520496e632e3110300e060355040b1307416e64726f69643110300e06035504031307416e64726f6964820900c2e08746644a308d300c0603551d13040530030101ff300d06092a864886f70d010104050003820101006dd252ceef85302c360aaace939bcff2cca904bb5d7a1661f8ae46b2994204d0ff4a68c7ed1a531ec4595a623ce60763b167297a7ae35712c407f208f0cb109429124d7b106219c084ca3eb3f9ad5fb871ef92269a8be28bf16d44c8d9a08e6cb2f005bb3fe2cb96447e868e731076ad45b33f6009ea19c161e62641aa99271dfd5228c5c587875ddb7f452758d661f6cc0cccb7352e424cc4365c523532f7325137593c4ae341f4db41edda0d0b1071a7c440f0fe9ea01cb627ca674369d084bd2fd911ff06cdbf2cfa10dc0f893ae35762919048c7efc64c7144178342f70581c9de573af55b390dd7fdb9418631895d5f759f30112687ff621410c069308a")}) == PackageManager.SIGNATURE_MATCH){
		allowed = true;
	}
// ByMe! END
```

Then install these APKs:

```javascript
adb install com.google.android.gms-6.6.03_(1681564-036)-6603036-minAPI9.apk
adb install GoogleLoginService.apk (Version: 4.4.4-1227136 ; Notice: this number seen in Application Manager in android)
adb install GoogleServicesFramework.apk (Like above line)
adb install com.android.vending_6.1.12-80441200_minAPI9(nodpi)ByMe.apk (Version: 6.6.03(1681564-036))
```

These are not necessary probably (After we added that signature), but do it to sure:

```javascript
adb shell pm grant com.google.android.gms android.permission.INTERACT_ACROSS_USERS
adb shell pm grant com.google.android.gms android.permission.READ_LOGS
adb shell pm grant com.google.android.gms android.permission.WRITE_SECURE_SETTINGS
//adb shell pm grant com.google.android.gms android.permission.ACCESS_DOWNLOAD_MANAGER
//adb shell pm grant com.google.android.gms android.permission.CHANGE_CONFIGURATION
//adb shell pm grant com.google.android.gms android.permission.MANAGE_USERS
```

Or

Add these to your init file:

```javascript
#ByMe
pm grant com.google.android.gms android.permission.INTERACT_ACROSS_USERS;
pm grant com.google.android.gms android.permission.READ_LOGS;
pm grant com.google.android.gms android.permission.WRITE_SECURE_SETTINGS;
pm grant com.google.android.gms android.permission.WRITE_SECURE_SETTINGS;
#pm grant com.google.android.gms android.permission.ACCESS_DOWNLOAD_MANAGER;
#pm grant com.google.android.gms android.permission.CHANGE_CONFIGURATION;
###
```
If above solution doesn't work, to add extra permission to these prebuilt applications:
```javascript
include $(CLEAR_VARS)
LOCAL_MODULE_TAGS := optional
LOCAL_MODULE := apkname.apk
LOCAL_MODULE_CLASS := APPS
LOCAL_PRIVILEGED_MODULE := true
LOCAL_CERTIFICATE := PRESIGNED
LOCAL_MODULE_PATH := $(TARGET_OUT)/priv-app
LOCAL_SRC_FILES := apkname.apk
include $(BUILD_PREBUILT)
```
This snipped code, copy APK to ``` /system/priv-app ```, to be eligible for system permissions.
For mor information: http://stackoverflow.com/questions/40397443/how-to-add-extra-permission-to-a-prebuilt-application-no-source-code-in-aosp


Now run google play store application; not anything else.
Login,
Enjoy!
