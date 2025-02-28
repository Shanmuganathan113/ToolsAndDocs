
import java.io.FileInputStream;
import java.io.IOException;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

public class FileChecksumComparer {

    public static String getFileChecksum(String filePath, String algorithm) throws NoSuchAlgorithmException, IOException {
        MessageDigest digest = MessageDigest.getInstance(algorithm);
        try (FileInputStream fis = new FileInputStream(filePath)) {
            byte[] byteArray = new byte[8192];
            int bytesRead;
            while ((bytesRead = fis.read(byteArray)) != -1) {
                digest.update(byteArray, 0, bytesRead);
            }
        }
        byte[] hashBytes = digest.digest();
        
        // Convert byte array to hexadecimal string
        StringBuilder hexString = new StringBuilder();
        for (byte b : hashBytes) {
            hexString.append(String.format("%02x", b));
        }
        return hexString.toString();
    }

    public static boolean areFilesIdentical(String file1, String file2, String algorithm) {
        try {
            String checksum1 = getFileChecksum(file1, algorithm);
            String checksum2 = getFileChecksum(file2, algorithm);
            return checksum1.equals(checksum2);
        } catch (NoSuchAlgorithmException | IOException e) {
            e.printStackTrace();
            return false;
        }
    }

    public static void main(String[] args) {
        String filePath1 = "path/to/first/file.txt";  // Replace with actual path
        String filePath2 = "path/to/second/file.txt"; // Replace with actual path

        boolean isSame = areFilesIdentical(filePath1, filePath2, "SHA-256");
        System.out.println("Are the files identical? " + isSame);
    }
}
