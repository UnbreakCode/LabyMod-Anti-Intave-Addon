# LabyMod-Anti-Intave-Addon
A LabyMod Addon which will block the usage of the Resource Exploit in Intave

It is known,that Intave is using the Resource Exploit to check your files in the /versions/ folder for Hack files.
They are searching for specific MD5 hashes.

# How to?
Just install the Addon into LabyMod  ¯\_(ツ)_/¯

# What is the Resource Exploit?
The Resource Exploit is used to access the hard drive without any permission/knowledge of the user.
It is present in the Minecraft: Java Edition Version 1.8 - 1.8.x

```java
public void handle(S48PacketResourcePackSend packet) {
  String url = packet.getUrl();
  String hash = packet.getHash();  
  if (url.startsWith("level://")) {
    String urlWithoutPrefix = url.substring("level://".length());
    File savesDirectory = new File(gameController.mcDataDir, "saves");
    File resourceFile = new File(savesDirectory, urlWithoutPrefix);    
    if (resourceFile.isFile()) {
    	netManager.sendPacket(new C19PacketResourcePackStatus(hash, Action.ACCEPTED));
	// snip, loading the resource.
    } else {
    	this.netManager.sendPacket(new C19PacketResourcePackStatus(hash, Action.FAILED_DOWNLOAD));
    }
  } else {
    // snip, handling URLs.
  }
}
```
To trigger the exploit, servers simply need to send a Resource Pack Send packet using a URL that starts with level://. The game will try to load that file from the saves directory. If the file exists, a packet with status ACCEPTED is sent. If not, a packet FAILED_DOWNLOAD is sent instead.

Traversing directories is also possible using ../, putting the whole computer’s data at risk. Servers can check if a file exists on the player’s computer, enumerate users names … but maybe even worse

This exploit is invisible from the player’s perspective and can be triggered at any time while playing on a server. This exploit can be automated and accelerated by sending the packet in batch, the server knows that the responses will follow the same order. Even better, the client is generous enough to send back the hash – which is conveniently sent by the server, and can just be the checked path itself.

If the Exploit succeeded,the launcher will log:
```
[0913/140810:INFO:GameCallbacks.cpp(199)] game/bns (Client thread) warn Unable to parse metadata section of resourcepack: servers.dat java.util.zip.ZipException: error in opening zip file
```

