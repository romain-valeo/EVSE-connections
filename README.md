# How to configure your EVSE connections?

| Author                 | Modification        | Date      |
| ---------------------- | ------------------- | ------------------- |
| Romain MUNIER          | Initial Doc         | 14/11/24            |

This guide explains how to configure EVSE connections with different usecases.\

> [!NOTE]  
> Nothing here so far

# Nominal devices configuration :

![Static testing configuration](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-static-testing.jpg "Static testing configuration")





1. Open a terminal and go to "apt" folder
```
cd /etc/apt/
```
2. Create a backup folder
```
mkdir landscape-backup
```
3. Move landscape files from the original folder to the "landscape-backup" folder
```
sudo mv sources.list.d/land* landscape-backup/   
```   
