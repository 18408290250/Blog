### IDEA解决sun.misc.BASE64Encoder找不到jar包的解决方法

sun.misc.BASE64Encoder 不建议使用java.sun自带包中的内容

import sun.misc.BASE64Encoder; 
import sun.misc.BASE64Decoder;


在项目中，设计到64位编码的。有时开发会用到JDK中自带的BASE64工具。但sun公司是建议不这样做的。尤其是更新了JDK版本，项目甚至还存在保存的信息。 
可引用 import org.apache.commons.codec.binary.Base64;进行替换


原来使用的JDK自带jar包中的

return new  BASE64Encoder().encode(encrypted);

替换为

    import org.apache.commons.codec.binary.Base64;
    return Base64.encodeBase64String(encrypted);

将
byte[] encrypted1 = new BASE64Decoder().decodeBuffer(text); 

替换为

    import org.apache.commons.codec.binary.Base64;
    byte[] encrypted1 =Base64.decodeBase64(text);   



### JDK12 IDea 引进DatatypeConverter失败
        MessageDigest md = MessageDigest.getInstance("MD5");
        md.update(str.getBytes());
        byte[] digest = md.digest();
        return DatatypeConverter.printHexBinary(digest).toUpperCase();