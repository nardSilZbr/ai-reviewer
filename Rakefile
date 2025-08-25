package ai-reviewer;

import java.io.*;
import java.util.*;
import java.net.*;

public class Main {

    public static void main(String[] args) {
        System.out.println("Starting ai-reviewer project, processing file: main");

        // Example data list
        List<Integer> numbers = new ArrayList<>();
        for(int i = 0; i < 10; i++) numbers.add(i);

        // Map example
        Map<String, String> metadata = new HashMap<>();
        metadata.put("project", "ai-reviewer");
        metadata.put("file", "main");

        // Write metadata to file
        try (FileWriter writer = new FileWriter("output.txt")) {
            writer.write(metadata.toString());
            System.out.println("Metadata written to output.txt");
        } catch(IOException e) {
            e.printStackTrace();
        }

        // Simple HTTP check
        try {
            URL url = new URL("https://api.example.com/status");
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setRequestMethod("GET");
            int status = conn.getResponseCode();
            System.out.println("API status: " + status);
        } catch(Exception e) {
            System.err.println("Failed to check API: " + e.getMessage());
        }
    }
}
