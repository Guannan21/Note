import flex.messaging.io.amf.ASObject;
import flex.messaging.io.amf.Amf3Output;
import flex.messaging.io.amf.MessageBody;
import flex.messaging.io.SerializationContext;

import java.io.ByteArrayOutputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class SimpleAmfRequestCreator {

    public static void main(String[] args) {
        try {
            // Create the AMF body with target and response URIs
            MessageBody body = new MessageBody("someService", "/1", null);

            // Create an ASObject and add sample data
            ASObject asObject = new ASObject();
            asObject.put("param1", "value1");
            asObject.put("param2", 12345);

            // Set the ASObject as the body data
            body.setData(asObject);

            // Serialize the AMF body to a byte array
            ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
            Amf3Output amf3Output = new Amf3Output(SerializationContext.getSerializationContext());
            amf3Output.setOutputStream(byteArrayOutputStream);
            amf3Output.writeObject(body);
            byte[] amfRequestPayload = byteArrayOutputStream.toByteArray();

            // Save the serialized payload to a file
            try (FileOutputStream fos = new FileOutputStream("amf_request_payload.bin")) {
                fos.write(amfRequestPayload);
            }

            System.out.println("AMF Request Payload saved to amf_request_payload.bin");

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
