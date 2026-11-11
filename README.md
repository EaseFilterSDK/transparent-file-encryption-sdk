# Transparent File Encryption Filter Driver SDK (EEFD)

[![FIPS 140-2 Compliant](https://img.shields.io/badge/FIPS%20140--2-Compliant-success)](#)
[![Encryption](https://img.shields.io/badge/Encryption-AES--128%2F192%2F256-blue)](#)
[![Platform](https://img.shields.io/badge/Platform-Windows-lightgray)](#)
[![Languages](https://img.shields.io/badge/Languages-C%23%20%7C%20C%2B%2B%20%7C%20Python%20%7C%20Java%20%7C%20Go%20%7C%20Rust-orange)](#)

The **EaseFilter Encryption Filter Driver (EEFD) SDK** provides a kernel-mode framework for Windows environments that enables developers to implement military-grade, transparent, "at-rest" data encryption dynamically at the file-system level. By intercepting I/O requests before they hit the physical disk, EEFD secures data seamlessly without requiring any modifications to existing enterprise applications.

---

## 🏗 Core Architecture

Unlike traditional volume-level disk encryption, EEFD provides granular, policy-based security operating via two main components:
![File Encryption](https://www.easefilter.com/Images/transparent-encryption-architecture.jpg)

*   **Kernel-Mode Filter Driver (`EaseFlt.sys`)**: Sits between the Windows I/O Manager and the file system. It intercepts I/O Request Packets (IRPs) for read/write operations, enforcing encryption policies and executing cryptographic transformations in real-time.
*   **User-Mode API Library (`FilterAPI.dll`)**: A managed/unmanaged library that allows developers to define filter rules, manage encryption keys, and register I/O callbacks from high-level applications.
 
## The advantages of the EaseFilter Encryption SDK:
1. **Completely transparent file encryption in file system level.** The encryption process was done in the write IO request, the decryption process was done in the read IO request, there are no extra IO request needed, it is completely transparent to the applications or users, the encryption or decryption doesn't require the application to interfere. 
2. **Keep encrypted file on disk all the time.** Since the decryption data only was kept in memory, there are no extra file created for the decryption.
3. **Authorized specific processes or users to read the encrypted file.** EaseFilter Encryption SDK was implemented with Isolation Mini Filter Driver. When the process or the user was authorized to access the encrypted file, the filter driver will decrypt the data in memory during the read request, so the authorized process can get the clear data back, or it will get the raw encrypted data. When the encryption filter driver is turned off, the application will always get the encrypted raw data.
4. **Random block level decryption.** You can decrypt the blocks of the file, you don't need to decrypt the whole file for all the time. For large encrypted file, if you only want to read the blocks of the data, it will improve the performance dramatically.
5. **High encryption or decryption performance.** EaseFilter Encryption Engine utilizes the US FIPS 140-2 compliant Microsoft CNG libraries, it can support AES-NI (or the Intel Advanced Encryption Standard New Instructions; AES-NI), at an algorithm level AES-NI provides significant speedup of AES. For non-Parallel modes of AES operation (CBC encrypt), AES-NI can provide 2-3 fold gain in performance over a completely software encryption. For parallel modes of AES operation (CBC-decrypt, CTR), AES-NI can provide 10x improvement over a completely software encryption.
   

## The products you can develop with EaseFilter Encryption SDK:

1. **Data encryption at rest.** Encryption at rest prevents the attacker from accessing the unencrypted data by ensuring the data is encrypted when on disk.
Data protection.

2. **Document encryption.** File encryption is very important step for data protection, only the authorized users or processes can read the encrypted data, or will get the raw encrypted data.

3. **Data loss prevention.** To prevent the data breach, your data is encrypted all the time, even your data was lost and found in an unauthorized place, they are protected against the unauthorized access.

4. **Secure file sharing with DRM.**
Encrypted your files with digital rights management data embedded into the encrypted header, protect, track and control your encrypted files anywhere anytime, you can grant or revoke the access control to any user at any time even the files were shared.

## Common Use Cases & Code Examples

### 1. Data at Rest Encryption
Ensure all files written to a specific directory are immediately and transparently encrypted on the physical disk.

```csharp
// Encrypt all files within the SecureData directory
FileFilter filter = new FileFilter("C:\\SecureData\\*");
filter.EnableEncryption = true;

// Assign the AES key (in production, fetch this dynamically from a secure KMS)
filter.EncryptionKey = Encoding.UTF8.GetBytes("Your32ByteSuperSecretKeyHere1234");
filterControl.AddFilter(filter);
```

### 2. Insider Threat Protection
Prevent unauthorized internal processes or users from accessing clear text. Isolation Minifilter Technology ensures only approved apps get the decrypted view.

```csharp
FileFilter filter = new FileFilter("C:\\Finance\\*");
filter.EnableEncryption = true;
filter.EncryptionKey = mySecureKey;

// Default: Deny decrypted read access to ALL processes (they will see raw cipher text)
filter.AccessFlags = FilterAPI.ALLOW_MAX_ACCESS_RIGHT & ~FilterAPI.ALLOW_ENCRYPTED_READ;

// Whitelist Microsoft Word to receive the decrypted clear text view
filter.AddProcessRight("WINWORD.exe", FilterAPI.ALLOW_MAX_ACCESS_RIGHT);

// Whitelist a specific Active Directory user to read decrypted data
filter.userAccessRightList.Add("DomainName\\HR_Manager", FilterAPI.ALLOW_MAX_ACCESS_RIGHT);

filterControl.AddFilter(filter);
```

### 3. Ransomware Protection
Combine transparent encryption with strict access controls to block untrusted binaries from modifying, deleting, or encrypting your already-secured data.

```csharp
FileFilter filter = new FileFilter("C:\\ProtectedBackups\\*");
filter.EnableEncryption = true;
filter.EncryptionKey = mySecureKey;

// Block files from being renamed or deleted by ANY process
filter.AccessFlags = FilterAPI.ALLOW_MAX_ACCESS_RIGHT & ~FilterAPI.ALLOW_FILE_DELETE  & ~FilterAPI.ALLOW_FILE_RENAME;

// Whitelist your authorized backup agent to allow writing and modification
filter.AddProcessRight("VeeamAgent.exe", FilterAPI.ALLOW_MAX_ACCESS_RIGHT);

filterControl.AddFilter(filter);
```

---

## A C# Auto File Encryption Example
With the C# auto file encryption example, you can setup an encryption folder in computer A, configure the authorized processes, users who can read the encrypted file, then you can setup the decryption folder in computer B, you can copy the encrypted file to this folder, and configure the authorized processes which can read the encrypted files in the decryption folder.

![File Encryption Demo](https://www.easefilter.com/images/autoencryptdemo.png)

[Read more about auto file encryption example](https://www.easefilter.com/Forums_Files/AutoFileEncryption.htm)

## EaseFilter File System Filter Driver SDK Reference
| Product Name | Description |
| --- | --- |
| [Cloud File System SDK](https://www.easefilter.com/cloud/cloud-file-system-sdk.htm) | EaseFilter Cloud File System SDK Introduction. |
| [CloudTier Storage Tiering SDK](https://www.easefilter.com/cloud/storage-tiering-sdk.htm) | EaseFilter Storage Tiering Filter Driver SDK Introduction. |
| [File Monitor SDK](https://www.easefilter.com/kb/file-monitor-filter-driver-sdk.htm) | EaseFilter File Monitor Filter Driver SDK Introduction. |
| [File Control SDK](https://www.easefilter.com/kb/file-control-file-security-sdk.htm) | EaseFilter File Control Filter Driver SDK Introduction. |
| [File Encryption SDK](https://www.easefilter.com/kb/transparent-file-encryption-filter-driver-sdk.htm) | EaseFilter Transparent File Encryption Filter Driver SDK Introduction. |
| [Registry Filter SDK](https://www.easefilter.com/kb/registry-filter-drive-sdk.htm) | EaseFilter Registry Filter Driver SDK Introduction. |
| [Process Filter SDK](https://www.easefilter.com/kb/process-filter-driver-sdk.htm) | EaseFilter Process Filter Driver SDK Introduction. |
| [EaseFilter SDK Programming](https://www.easefilter.com/kb/programming.htm) | EaseFilter Filter Driver SDK Programming. |

## EaseFilter SDK Sample Projects
| Sample Project | Description |
| --- | --- |
| [CloudTier Storage Tiering Demo](https://www.easefilter.com/cloud/cloudtier-storage-tiering-demo.htm) | A HSM File System Filter Driver Demo. |
| [CloudTier S3 Tiering Demo](https://www.easefilter.com/cloud/cloudtier-s3-intelligent-tiering-demo.htm) | CloudTier S3 Intelligent Tiering Demo. |
| [Cloud File DR S3 Demo](https://www.easefilter.com/cloud/cloud-file-dr-demo.htm) | Cloud File DR S3 Demo. |
| [Amazon S3 File Explorer Demo](https://www.easefilter.com/cloud/s3-browser-demo.htm) | Amazon S3 File Explorer Demo. |
| [Auto File DRM Encryption](https://www.easefilter.com/kb/auto-file-drm-encryption-tool.htm) | Auto file encryption with DRM data embedded. |
| [Transparent File Encrypt](https://www.easefilter.com/kb/AutoFileEncryption.htm) | Transparent on access file encryption. |
| [Secure File Sharing with DRM](https://www.easefilter.com/kb/DRM_Secure_File_Sharing.htm) | Secure encrypted file sharing with digital rights management. |
| [File Monitor Example](https://www.easefilter.com/kb/file-monitor-demo.htm) | Monitor file system I/O in real time, tracking file changes. |
| [File Protector Example](https://www.easefilter.com/kb/file-protector-demo.htm) | Prevent sensitive files from being accessed by unauthorized users or processes. |
| [FolderLocker Example](https://www.easefilter.com/kb/FolderLocker.htm) | Lock file automatically in a FolderLocker. |
| [Process Monitor](https://www.easefilter.com/kb/Process-Monitor.htm) | Monitor the process creation and termination, block unauthorized process running. |
| [Registry Monitor](https://www.easefilter.com/kb/RegMon.htm) | Monitor the Registry activities, block the modification of the Registry keys. |
| [Secure Sandbox Example](https://www.easefilter.com/kb/Secure-Sandbox.htm) |A secure sandbox example, block the processes accessing the files out of the box. |
| [FileSystemWatcher Example](https://www.easefilter.com/kb/FileSystemWatcher.htm) | File system watcher, logging the file I/O events. |
| [ZeroTrust Example](https://www.easefilter.com/kb/zero-trust-file-access-control-demo.htm) | Zero trust file access control with encryption feature. |

## Filter Driver Reference

* [Understand MiniFilter Driver](https://www.easefilter.com/kb/understand-minifilter.htm)
* [Understand File I/O](https://www.easefilter.com/kb/File_IO.htm)
* [Understand I/O Request Packets(IRPs)](https://www.easefilter.com/kb/understand-irps.htm)
* [Filter Driver Developer Guide](https://www.easefilter.com/kb/DeveloperGuide.htm)
* [MiniFilter Filter Driver Framework](https://www.easefilter.com/kb/minifilter-framework.htm)
* [Isolation Filter Driver](https://www.easefilter.com/kb/Isolation_Filter_Driver.htm)

## License

This repository contains two distinct licensing categories:

1. **Wrapper/Implementation Code (MIT License):** 
   The source code, scripts, and configuration files provided directly in this repository are licensed under the [MIT License](LICENSE). You are free to modify, distribute, and use this specific integration code.

2. **EaseFilter SDK (Commercial License):**
   This project requires the EaseFilter SDK (including its binaries, filter drivers, and core libraries) to function. The EaseFilter SDK is **proprietary commercial software** and is strictly excluded from the MIT License.   
   To use the EaseFilter SDK for commercial purposes or in a production environment, you must obtain a valid commercial license directly from [EaseFilter](https://www.easefilter.com/license.pdf).


## Support
If you have questions or need help, please contact support@easefilter.com 

[Home](https://www.easefilter.com/) | [Solution](https://www.easefilter.com/solutions.htm) | [Download](https://www.easefilter.com/download.htm) | [Demos](https://www.easefilter.com/online-fileio-test.aspx) | [Blog](https://blog.easefilter.com/) | [Programming](https://www.easefilter.com/kb/programming.htm)
