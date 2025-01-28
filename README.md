# javasas


import com.sas.iom.SASIOMDefs.StringSeqHolder;
import com.sas.iom.SAS.ILanguageService;
import com.sas.iom.SAS.IWorkspace;
import com.sas.iom.WorkspaceFactory;

import java.util.Properties;

public class SASJobStatusChecker {

    public static void main(String[] args) {
        String serverHost = "your.server.com";  // Replace with SAS server
        int serverPort = 8591;  // Adjust the port if necessary
        String username = "your_username";
        String password = "your_password";
        String sasCode = "DATA test; SET sashelp.class; RUN;";  // Example SAS Job

        try {
            // Establish a connection to the SAS workspace
            Properties props = new Properties();
            props.setProperty("host", serverHost);
            props.setProperty("port", String.valueOf(serverPort));
            props.setProperty("userName", username);
            props.setProperty("password", password);

            WorkspaceFactory factory = new WorkspaceFactory();
            IWorkspace workspace = factory.createWorkspace(props);
            ILanguageService langService = workspace.LanguageService();

            // Submit the SAS Job
            langService.Submit(sasCode);
            System.out.println("SAS job submitted successfully!");

            // Monitor execution status
            StringSeqHolder log = new StringSeqHolder();
            boolean isRunning = true;

            while (isRunning) {
                Thread.sleep(2000); // Poll every 2 seconds
                langService.FlushListLines(log);
                
                for (String line : log.value) {
                    System.out.println(line);
                    if (line.contains("ERROR")) {
                        System.out.println("SAS Job encountered an error.");
                        isRunning = false;
                    } else if (line.contains("NOTE: The DATA step has been executed")) {
                        System.out.println("SAS Job completed successfully!");
                        isRunning = false;
                    }
                }
            }

            // Retrieve execution log
            langService.FlushLogLines(0, log);
            System.out.println("Execution Log:");
            for (String line : log.value) {
                System.out.println(line);
            }

            // Close the workspace connection
            workspace.Close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

