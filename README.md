
The attached program uses the smbj library and logs in with NTLM, I need to log in with Kerberos. 
The attached program works perfectly as written with NTLM authentication.




 public class smbj_copy {
   public static void main (String[] args) throws Exception {
      smbj_copy sc = new smbj_copy();
      sc.copyFileTo(new java.io.File("test.txt"),"Departments", "TEST2","SERVER", "test.txt");
    
  }

   public void copyFileTo (java.io.File fileToCopy, String shareName, String folderDest, String serverName, String newFileName) throws Exception {

      SMBClient client = new SMBClient();
      Connection connection = null;
      Session session = null;
      java.io.FileInputStream fis = null;
      com.hierynomus.smbj.share.File file = null;  

      try {
         connection = client.connect(serverName); 
         AuthenticationContext ac = new AuthenticationContext("username", "password".toCharArray(), "domain");
         session = connection.authenticate(ac);       
         DiskShare share = (DiskShare) session.connectShare(shareName);
            fis = new java.io.FileInputStream (fileToCopy);
            byte[] bf = new byte[fis.available()];
            fis.read(bf);
         if(share.folderExists(folderDest)) {
            file = share.openFile(folderDest+"\\"+newFileName, EnumSet.of(AccessMask.GENERIC_WRITE), null, SMB2ShareAccess.ALL, SMB2CreateDisposition.FILE_OVERWRITE_IF, null);
            int byresWritten = file.write(new InputStreamByteChunkProvider(new ByteArrayInputStream(bf)));
            file.flush();
            file.close();
        } else {
                System.out.println("Userul "+folderDest+ " nu exista!");
                }

         fis.close();
         session.close();
         connection.close();
      } catch (Exception e) {
         try {
            if (connection != null) { connection.close();  }
            if (session != null) { session.close();  }
            if (fis != null) { fis.close(); }
            if (file != null) { file.close(); }
         } catch (Exception e1) {
            System.out.println("Error closing objects "+e);
         }
         throw e;
        }
          
   }

 }
