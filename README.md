To update the automated run value with a new timestamp (1 hour added), modify the automatedRun() method to update the file instead of setting it to null.


---

Updated automatedRun() Method

@Scheduled(fixedRate = 900000) // Every 15 minutes
public void automatedRun() {
    Map<String, String> data = readFile();
    if (data.containsKey("Automated run") && !data.get("Automated run").isEmpty()) {
        String oldTimestamp = data.get("Automated run").trim();
        String newTimestamp = generateReport(oldTimestamp); // Get new date with 1 hour added
        updateFile("Automated run", newTimestamp); // Update file with new value
    }
}


---

Updated generateReport() Method

Now it returns the new timestamp instead of just printing it:

private String generateReport(String timestamp) {
    try {
        LocalDateTime dateTime = LocalDateTime.parse(timestamp, FORMATTER);
        LocalDateTime updatedTime = dateTime.plusHours(1);
        String newTimestamp = updatedTime.format(FORMATTER);
        System.out.println("Generated report for: " + newTimestamp);
        return newTimestamp;
    } catch (Exception e) {
        System.err.println("Error parsing date: " + timestamp);
        return timestamp; // Return old timestamp if error occurs
    }
}


---

What Changes?

Instead of setting Automated run to null, we update it with a new timestamp (1 hour later).

generateReport() now returns the updated timestamp.

The new timestamp is saved back to the file.



---

Example Before Update

Automated run : 28-Feb-2025 14:00:000

After First Run

Generated report for: 28-Feb-2025 15:00:000

Automated run : 28-Feb-2025 15:00:000

After Next Run (15 mins later)

Generated report for: 28-Feb-2025 16:00:000

Automated run : 28-Feb-2025 16:00:000

Now, the automated timestamp keeps increasing with each execution. Let me know if you need any more changes!

