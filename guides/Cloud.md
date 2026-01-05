# Cloud

[<img align="right" width=150px src='../res/rackete_2.png'></img>](../README.md)

This is a helper for Cloud-Systems. Use it as reference.

Table of Contents:
- [Up-/Downloading big or many Files](#up-downloading-big-or-many-files)



<br><br>

---

### Up-/Downloading big or many Files

Cloud-Systems are great, they make your files accessable over multiple devices and automatically make Backups of important files and promise that internally your data is saved safely and with backups.<br>
You upload every image and maybe project files and on some point you might want to store them on a local storage and then you notice that download many files or/and big files is a huge problem and most likely does not work because of timeouts and other problems...it just not works. The same applies to uploading.<br>
But what does this cloud storage is good for when big data can only be accessed in small groups? Donwloading all your pictures would takes several days or weeks with such a method.

Maybe there are multiple solutions but I found following solution pretty comfortable. We use a software which controls and watch on the download or upload and handles the re-up-/download, so that we can upload and download big and many files.

I used the `rclone` software for that. As said, there might be other software and other methods to do, and it might be clever to inform yourself if there is now a better solution, but now let me introduce you to `rclone` and how you can use it.

Here it is with the example of OneDrive.

1. Installation
    - Donwload the execution file from https://rclone.org/downloads/ and unpack it to `C:\rclone` or whever you want to have it -> Your `Arch-OS` can be found in Your `Settings > System > Devicespecifications > Systemtype` (64-bit...) and there is also the processor name/brand. Else you can also check your system with [HWiNFO](https://www.hwinfo.com/download/).
    - Or on Linux: `curl https://rclone.org/install.sh | sudo bash`
    - Check installation with opening your CMD and type `rclone --version`
2. Setup OneDrive
    - Start your CMD and type `rclone config`
    - Choose `n` (create new remote) from the menu which opened
    - name it `onedrive` or something like that
    - Choose `Microsoft OneDrive` as storage
    - Follow the *authentification* (most likely brwoser opens, ...)
    - Save, and finish is the setup
    - Test it with listing your files: `rclone ls onedrive:`
3. Upload
    ```bash
    rclone copy "C:\my_folder" onedrive:BackupOrdner --progress --transfers=8 --checkers=16 --copy-links --create-empty-src-dirs
    ```
    - `copy` = transfer data (without deleting the original) -> in this case: upload/download 
    - `"C:\my_folder"` = source folder
    - `onedrive:BackupOrdner` = target folder
    - `--progress` = progress view
    - `--transfers=8` = how many files at the same time should be uploaded
    - `--checkers=16` = how many checks are allowed to run at one time
    - Example:
        ```bash
        rclone copy "D:\BACKUP\GitHub\2026_01_05 GitHub" "onedrive:BACKUP/Github/2025_01_05 Github" --progress --transfers=8 --checkers=16 --copy-links --create-empty-src-dirs
        ```
4. Download
    ```bash
    rclone copy onedrive:BackupOrdner "C:\my_folder" --progress --transfers=8 --checkers=16 --copy-links --create-empty-src-dirs
    ```
5. Synchronize (make the target same as the source, therefore delete/add files until it is equal to the source)
    ```bash
    rclone sync "C:\my_folder" onedrive:BackupOrdner --progress --transfers=8 --checkers=16
    ```
    For that you can add `--dry-run` to just show you what would be changed without really doing it.






