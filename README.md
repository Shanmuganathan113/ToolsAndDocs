import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Service;

import java.io.*;
import java.nio.file.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.*;

@Service
public class ReportService {

    private static final String FILE_PATH = "C:/data/report_schedule.txt";
    private static final DateTimeFormatter FORMATTER = DateTimeFormatter.ofPattern("dd-MMM-yyyy HH:mm:ss");

    @Scheduled(fixedRate = 3600000) // Every 1 hour
    public void manualRun() {
        Map<String, String> data = readFile();
        if (data.containsKey("Manual run") && !data.get("Manual run").isEmpty()) {
            String[] timestamps = data.get("Manual run").split(",");
            for (String timestamp : timestamps) {
                generateReport(timestamp.trim());
            }
            updateFile("Manual run", ""); // Reset manual run
        }
    }

    @Scheduled(fixedRate = 900000) // Every 15 minutes
    public void automatedRun() {
        Map<String, String> data = readFile();
        if (data.containsKey("Automated run") && !data.get("Automated run").isEmpty()) {
            generateReport(data.get("Automated run").trim());
            updateFile("Automated run", null); // Reset automated run
        }
    }

    private void generateReport(String timestamp) {
        try {
            LocalDateTime dateTime = LocalDateTime.parse(timestamp, FORMATTER);
            LocalDateTime updatedTime = dateTime.plusHours(1);
            System.out.println("Generated report for: " + updatedTime.format(FORMATTER));
        } catch (Exception e) {
            System.err.println("Error parsing date: " + timestamp);
        }
    }

    private Map<String, String> readFile() {
        Map<String, String> data = new HashMap<>();
        try {
            List<String> lines = Files.readAllLines(Paths.get(FILE_PATH));
            for (String line : lines) {
                String[] parts = line.split(":", 2);
                if (parts.length == 2) {
                    data.put(parts[0].trim(), parts[1].trim());
                }
            }
        } catch (IOException e) {
            System.err.println("Error reading file: " + e.getMessage());
        }
        return data;
    }

    private void updateFile(String key, String newValue) {
        try {
            List<String> lines = Files.readAllLines(Paths.get(FILE_PATH));
            List<String> updatedLines = new ArrayList<>();
            for (String line : lines) {
                if (line.startsWith(key)) {
                    updatedLines.add(key + " : " + (newValue == null ? "" : newValue));
                } else {
                    updatedLines.add(line);
                }
            }
            Files.write(Paths.get(FILE_PATH), updatedLines);
        } catch (IOException e) {
            System.err.println("Error updating file: " + e.getMessage());
        }
    }
}
