# Online-Job-Portal
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class JobPortal {
    private List<User> users = new ArrayList<>();
    private List<Job> jobs = new ArrayList<>();
    private User currentUser = null;

    // Main Class to Run Job Portal
    public static void main(String[] args) {
        JobPortal portal = new JobPortal();
        portal.run();
    }

    // Run the portal
    private void run() {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Welcome to the Online Job Portal!");

        while (true) {
            System.out.println("\n1. Register\n2. Login\n3. Post Job\n4. View Jobs\n5. Apply for Job\n6. View Applications\n7. Logout\n8. Exit");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1 -> registerUser(scanner);
                case 2 -> loginUser(scanner);
                case 3 -> postJob(scanner);
                case 4 -> viewJobs();
                case 5 -> applyForJob(scanner);
                case 6 -> viewApplications();
                case 7 -> logout();
                case 8 -> { 
                    System.out.println("Exiting... Goodbye!"); 
                    scanner.close(); 
                    return;
                }
                default -> System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    // User Registration
    private void registerUser(Scanner scanner) {
        System.out.print("Enter name: ");
        String name = scanner.nextLine();
        System.out.print("Enter email: ");
        String email = scanner.nextLine();
        System.out.print("Enter password: ");
        String password = scanner.nextLine();
        System.out.print("Are you an employer? (yes/no): ");
        boolean isEmployer = scanner.nextLine().equalsIgnoreCase("yes");

        users.add(new User(name, email, password, isEmployer));
        System.out.println("User registered successfully!");
    }

    // User Login
    private void loginUser(Scanner scanner) {
        System.out.print("Enter email: ");
        String email = scanner.nextLine();
        System.out.print("Enter password: ");
        String password = scanner.nextLine();

        currentUser = users.stream()
                           .filter(user -> user.login(email, password))
                           .findFirst()
                           .orElse(null);

        System.out.println(currentUser != null ? "Login successful!" : "Invalid email or password.");
    }

    // Post a Job (Employer only)
    private void postJob(Scanner scanner) {
        if (currentUser != null && currentUser.isEmployer()) {
            System.out.print("Enter job title: ");
            String title = scanner.nextLine();
            System.out.print("Enter job description: ");
            String description = scanner.nextLine();

            jobs.add(new Job(title, description, currentUser));
            System.out.println("Job posted successfully!");
        } else {
            System.out.println("Only employers can post jobs.");
        }
    }

    // View all jobs
    private void viewJobs() {
        if (jobs.isEmpty()) {
            System.out.println("No jobs available.");
        } else {
            jobs.forEach(job -> System.out.println("Job Title: " + job.getTitle() + " | Posted by: " + job.getEmployer().getName()));
        }
    }

    // Apply for a Job (Job Seekers only)
    private void applyForJob(Scanner scanner) {
        if (currentUser != null && !currentUser.isEmployer()) {
            System.out.print("Enter job title to apply: ");
            String jobTitle = scanner.nextLine();

            Job job = jobs.stream()
                          .filter(j -> j.getTitle().equalsIgnoreCase(jobTitle))
                          .findFirst()
                          .orElse(null);

            if (job != null) {
                currentUser.applyForJob(job);
                System.out.println("Application submitted!");
            } else {
                System.out.println("Job not found.");
            }
        } else {
            System.out.println("Only job seekers can apply for jobs.");
        }
    }

    // View Applications (Employers only)
    private void viewApplications() {
        if (currentUser != null && currentUser.isEmployer()) {
            jobs.stream()
                .filter(job -> job.getEmployer().equals(currentUser))
                .forEach(job -> {
                    System.out.println("Job Title: " + job.getTitle());
                    System.out.println("Applicants:");
                    job.getApplicants().forEach(applicant -> System.out.println(" - " + applicant.getName()));
                });
        } else {
            System.out.println("Only employers can view applications.");
        }
    }

    // Logout
    private void logout() {
        currentUser = null;
        System.out.println("Logged out successfully!");
    }

    // Inner Class: User
    class User {
        private String name;
        private String email;
        private String password;
        private boolean isEmployer;
        private List<Job> applications;

        public User(String name, String email, String password, boolean isEmployer) {
            this.name = name;
            this.email = email;
            this.password = password;
            this.isEmployer = isEmployer;
            this.applications = new ArrayList<>();
        }

        public String getName() { return name; }
        public boolean isEmployer() { return isEmployer; }
        
        public boolean login(String email, String password) {
            return this.email.equals(email) && this.password.equals(password);
        }

        public void applyForJob(Job job) {
            applications.add(job);
            job.addApplicant(this);
        }
    }

    // Inner Class: Job
    class Job {
        private String title;
        private String description;
        private User employer;
        private List<User> applicants;

        public Job(String title, String description, User employer) {
            this.title = title;
            this.description = description;
            this.employer = employer;
            this.applicants = new ArrayList<>();
        }

        public String getTitle() { return title; }
        public User getEmployer() { return employer; }

        public void addApplicant(User user) {
            applicants.add(user);
        }

        public List<User> getApplicants() { return applicants; }
    }
}
