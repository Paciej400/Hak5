## Screen Crab: Student ID Grabber

This project was developed by **Miłosz Obrębski** for the **PING Scientific Club** at Gdańsk University of Technology. Project demonstrates how sensitive data can be intercepted by a device that appears as a harmless video adapter to the operating system.

---

### Motivation

The selection of this project was driven by a desire to explore physical security and the practical application of Man-in-the-Middle (MitM) video proxies. 

### Overview

The project utilizes the **Hak5 Screen Crab**, a man-in-the-middle video proxy that sits between a computer and a monitor. It captures screen data and streams it over Wi-Fi without triggering OS alerts.

### The Python Script

A custom Python script automates the collection of sensitive data:

* **API Integration**: Connects to the **Cloud C2** platform to fetch images.


* **Stealth Detection**: Uses **OpenCV** to detect the university logo on the login page.


* **Automated Capture**: When the logo is detected, it saves the screenshot (containing the student ID) for analysis.

### Key learnings

The project provided an ability to learn about Hak5 hardware and gain a comprehensive understanding of what the Screen Crab can be utilized for, specifically as a man-in-the-middle video proxy for screen capture and remote streaming. Furthermore, the custom script written for this project provided experience in processing retrieved data—in this case, images exfiltrated from the target computer.

---

### Educational Use & Disclaimer

> [!IMPORTANT]
> The author of this project is not responsible for any non-educational use cases. This tool should only be used within the boundaries of the law and for educational purposes.
> 
>