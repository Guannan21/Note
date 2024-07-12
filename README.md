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




import flex.messaging.io.amf.ASObject;
import flex.messaging.io.amf.Amf3Output;
import flex.messaging.io.amf.Message;
import flex.messaging.io.amf.MessageBody;
import flex.messaging.io.amf.MessageHeader;
import flex.messaging.io.SerializationContext;

import java.io.ByteArrayOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;

public class SimpleAmfRequestCreator {

    public static void main(String[] args) {
        String filePath = "path/to/your/file.txt"; // Replace with the path to your file

        try {
            // Read the content of the file into a byte array
            byte[] fileContent = Files.readAllBytes(Paths.get(filePath));

            // Create a new AMF message
            Message amfMessage = new Message();

            // Add a header (if necessary)
            MessageHeader header = new MessageHeader("operation", false, null);
            amfMessage.addHeader(header);

            // Create the body with target and response URIs
            MessageBody body = new MessageBody("someService", "/1", null);

            // Create an ASObject and add sample data along with the file content
            ASObject asObject = new ASObject();
            asObject.put("param1", "value1");
            asObject.put("param2", 12345);
            asObject.put("fileContent", fileContent);

            // Set the ASObject as the body data
            body.setData(asObject);

            // Add the body to the AMF message
            amfMessage.addBody(body);

            // Serialize the AMF message to a byte array
            ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
            SerializationContext serializationContext = SerializationContext.getSerializationContext();
            serializationContext.legacyCollection = false; // Ensuring using AMF3
            Amf3Output amf3Output = new Amf3Output(serializationContext);
            amf3Output.setOutputStream(byteArrayOutputStream);
            amf3Output.writeObject(amfMessage);
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




