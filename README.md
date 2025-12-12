# 🚀 Multithreaded Web Crawler in Java

A fully parallel, depth-controlled web crawler built using **Java**, **JSoup**, **ExecutorService**, **Phaser**, **ConcurrentHashMap**, and **BlockingQueue**.  
This project demonstrates how to build a scalable, thread-safe crawler that extracts hyperlinks efficiently.

---

## 📌 Features

- Multithreaded crawling using a fixed thread pool  
- Depth-based crawling (prevents infinite recursion)  
- Thread-safe URL storage  
- Duplicate URL prevention  
- HTML parsing and link extraction using JSoup  
- Dynamic task synchronization using Phaser  
- Total crawl time measurement  
- Clean and modular architecture  

---

## 🛠️ Technologies Used

| Component | Purpose |
|----------|---------|
| **Java 17+** | Core language |
| **JSoup** | HTML fetching & parsing |
| **ExecutorService** | Thread pool for concurrent workers |
| **Phaser** | Tracks active crawl tasks dynamically |
| **ConcurrentHashMap** | Thread-safe visited URL storage |
| **LinkedBlockingQueue** | Thread-safe crawling queue |

---

## 📂 Project Structure

```bash
    src/main/java/org/example/
    │
    ├── WebCrawler.java # Entry point (setup, thread pool, phaser)
    ├── CrawlerTask.java # Worker task executed by threads
    ├── URLFetcher.java # JSoup-based HTML fetcher and link extractor
    └── URLStore.java # Thread-safe URL manager (queue + visited set)
```




# 🔍 How the Crawler Works

## 1️⃣ WebCrawler.java — Entry Point

- Takes user input:  
  - Starting URL  
  - Max depth  
  - Number of worker threads  
- Initializes:  
  - `URLStore`  
  - `URLFetcher`  
  - `Phaser(1)` → registers main thread  
  - `ExecutorService` (fixed thread pool)  
- Pushes starting URL into the queue  
- Submits the first crawling task (depth = 0)  
- Waits for all tasks to finish:  

```bash
    phaser.awaitAdvance(phaser.getPhase());
```

- Shuts down thread pool  
- Prints execution time  

---

## 2️⃣ URLStore.java — URL Management

Handles all URL storage using:

- **ConcurrentHashMap** → thread-safe visited set  
- **LinkedBlockingQueue** → safe, unbounded URL queue  

Methods:

- `addUrl(url)` — adds unique URL (avoids duplicates)  
- `getNextUrl()` — returns next URL from queue  
- `isEmptyQueue()` — checks if queue is empty  

---

## 3️⃣ URLFetcher.java — HTML Fetching & Parsing

- Fetches page HTML using JSoup (`Jsoup.connect().get()`)  
- Extracts all `<a href>` tags using CSS selector  
- Converts relative links to absolute links  
- Returns a **Set<String>** of unique extracted URLs  

---

## 4️⃣ CrawlerTask.java — Worker Logic

Each worker thread:

1. Fetches next URL from queue  
2. Validates depth & null URLs  
3. Extracts links from the page  
4. For every **new unique** link:
 - Add to URLStore  
 - Register new task in Phaser  
 - Submit new crawling task (with incremented depth)  
5. Finally deregisters itself from Phaser  

This ensures the system knows exactly when all tasks have completed.


# How to Run

### 1. Add JSoup Library
Download JSoup jar:  
https://jsoup.org/download

Add to classpath.


### 2. Compile

```bash
    javac -cp jsoup-1.17.2.jar -d out src/main/java/org/example/*.java
```



### 3. Run

```bash
    java -cp "out;jsoup-1.17.2.jar" org.example.WebCrawler

```



# 🚧 Current Limitations

- JSoup does not render JavaScript  
- No robots.txt validation  
- No domain restrictions  
- No rate limiting  
- Not designed for massive-scale crawling  

---

# 🔮 Future Enhancements

- Add allowed-domain filtering  
- Add polite crawling (delay between requests)  
- Retry logic for failed pages  
- Save crawled output to file or DB  
- Skip non-HTML content  
- Add CLI flags or GUI interface  

---

# 🤝 Contributing

Contributions are welcome!  
Feel free to open issues or submit pull requests.

---
