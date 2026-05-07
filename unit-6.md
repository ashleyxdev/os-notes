# Unit 6: File Management

## Topics Covered

1. File Concept
2. Access Methods
3. File Types
4. File Operations
5. Directory Structure
6. File System Structure
7. Allocation Methods (Contiguous, Linked, Indexed)
8. Free-Space Management (Bit Vector, Linked List, Grouping)
9. Directory Implementation (Linear List, Hash Table)
10. Linux File System
11. Android File System

---

## 📁 Topic 1: File Concept

### 1. Concept Explanation

A **file** is a named collection of related information stored on secondary storage (disk). It is the smallest unit of logical secondary storage — meaning the OS abstracts the physical storage details and presents data as files.

**Key Terms & Points:**

- **File** — A logical unit of information created by a user/process and stored persistently.
- **File Name** — Identifier for the file (e.g., `notes.txt`). Most OSes are case-sensitive or case-insensitive depending on the system.
- **File Attributes (Metadata):**
  - Name, Type, Location (pointer to device), Size, Protection (permissions), Time/Date, Owner
- **File Structure** — A file can be structured as:
  - **Byte sequence** (Unix/Linux) — no structure imposed by OS
  - **Record sequence** — fixed or variable-length records
  - **Tree structure** — records organized in a tree (older mainframe systems)
- **File is an abstraction** — it hides the complexity of how data is physically stored on disk.

---

### 2. Exam Question

> **"What is a file? Explain the attributes and structure of a file in an operating system."** *(6 marks)*

---

### 3. Model Answer

**Introduction:**
A file is a named, logical collection of related information recorded on secondary storage. The OS manages files through the file system, providing a uniform view of stored data independent of physical hardware.

**File Attributes:**

| Attribute | Description |
|---|---|
| Name | Human-readable identifier |
| Type | Extension indicating file kind (.txt, .exe) |
| Location | Pointer to file's location on device |
| Size | Current size in bytes/records |
| Protection | Read/Write/Execute permissions |
| Time & Date | Creation, modification, last-access time |
| Owner | User who created the file |

**File Structures:**
- **Byte Stream** — Unstructured; OS sees file as sequence of bytes (used in Unix/Linux)
- **Record Sequence** — File is a sequence of fixed/variable records
- **Tree of Records** — Records identified by a key field; used for fast retrieval

**File as Abstraction:**
The file concept abstracts physical disk blocks into a logical, user-friendly entity. The OS maps logical file addresses to physical disk addresses transparently.

**Conclusion:**
The file concept is fundamental to OS design — it provides a simple, uniform interface for data storage, hiding hardware complexities and enabling structured, persistent data management.

---

### 💡 Exam Tips
- Always **define file** first, then list attributes in a table — tables fetch more marks.
- Remember the mnemonic for attributes: **"NT-LSPTO"** → Name, Type, Location, Size, Protection, Time, Owner
- Examiners love when you mention that **Unix treats everything as a byte stream** — it shows depth.
- Don't confuse **file structure** (byte/record/tree) with **directory structure** — they're separate topics.

## 🔍 Topic 2: Access Methods

### 1. Concept Explanation

**Access methods** define how data stored in a file is **read or accessed**. Different applications require different ways of accessing file data, so the OS supports multiple access methods.

**Three Main Access Methods:**

### 🔹 1. Sequential Access
- Data is accessed **in order**, one record after another.
- Most common method (used by editors, compilers).
- Operations: `read next`, `write next`, `reset` (rewind to beginning)
- Think of it like a **cassette tape** — must rewind/forward to reach data.

```
| Block 1 | Block 2 | Block 3 | Block 4 |
   read →  →  →  →  →  →  →  →
```

### 🔹 2. Direct Access (Random Access)
- File is made of **fixed-length logical records**.
- Records can be read/written in **any order** — no need to follow sequence.
- Operations: `read n`, `write n`, `position to n`, where n = block number.
- Think of it like a **CD** — can jump to any track directly.
- Used in databases.

```
| Block 1 | Block 2 | Block 3 | Block 4 |
              ↑ jump directly to Block 2
```

### 🔹 3. Index Sequential Access (Indexed Access)
- Built **on top of direct access**.
- A separate **index file** is maintained containing pointers to various blocks.
- To find a record → search the index → use pointer to jump directly to the block.
- Used in large databases (e.g., ISAM — Indexed Sequential Access Method).

```
Index Table          File
┌─────────┐        ┌──────────┐
│ Key → 3 │───────▶│ Block 3  │
│ Key → 7 │───────▶│ Block 7  │
└─────────┘        └──────────┘
```

---

### 2. Exam Question

> **"Explain the different file access methods supported by an operating system with suitable diagrams."** *(6 marks)*

---

### 3. Model Answer

**Introduction:**
Access methods determine how records within a file are read and written. The OS provides different access methods to suit different application needs — from simple text editors to complex database systems.

**1. Sequential Access:**
- Records are accessed one by one in a fixed order.
- A pointer (current position) moves forward with each read/write.
- **Reset** operation returns pointer to the beginning.
- Example: Reading a text file line by line.

**2. Direct (Random) Access:**
- Any record can be accessed directly using its **block number**.
- No need to read preceding records.
- Based on the concept of a file as a **fixed-length record array**.
- Example: Database record retrieval by ID.

**3. Indexed Access:**
- Maintains an **index** of key-pointer pairs.
- To access a record: search index for key → follow pointer to block.
- Supports fast retrieval for large files.
- Example: ISAM (Indexed Sequential Access Method).

**Comparison Table:**

| Feature | Sequential | Direct | Indexed |
|---|---|---|---|
| Access Order | In order | Any order | Via index |
| Speed | Slow for large files | Fast | Fastest |
| Use Case | Text files | Databases | Large DBs |
| Complexity | Simple | Moderate | Complex |

**Conclusion:**
Choosing the right access method is critical for system performance. Sequential access suits simple file reading, direct access suits databases, and indexed access suits large, key-based retrieval systems.

---

### 💡 Exam Tips
- Always draw a **simple diagram** for each method — even ASCII-style diagrams earn marks.
- The **comparison table** at the end shows the examiner you understand differences clearly.
- Remember: **Indexed = Direct Access + Index Table** — it's an extension, not a separate concept.
- Keyword to remember: **"ISAM"** — always mention it for indexed access, it shows practical knowledge.

## 🗂️ Topic 3: File Types

### 1. Concept Explanation

**File types** indicate the **kind of data** a file contains and how it should be used. The OS and applications use file types to determine how to handle a file — whether to execute it, display it, compile it, etc.

File type is commonly indicated by the **file extension** (e.g., `.txt`, `.exe`) or by **magic numbers** stored in the file header.

---

**Ways to Indicate File Type:**
- **Extension** — suffix added to filename (`.c`, `.java`, `.mp3`) — used by Windows heavily
- **Magic Number** — first few bytes of the file indicate its type (used in Unix/Linux)
- **File Attribute** — separate metadata field storing type (used in older Mac OS)

---

**Common File Types:**

| File Type | Extension | Function |
|---|---|---|
| Executable | `.exe`, `.out` | Ready-to-run machine code |
| Object | `.obj`, `.o` | Compiled but not linked code |
| Source Code | `.c`, `.java`, `.py` | Human-readable program code |
| Text | `.txt`, `.doc` | Plain or formatted text data |
| Library | `.lib`, `.dll`, `.so` | Routines for use by programs |
| Archive | `.zip`, `.tar` | Grouped/compressed files |
| Multimedia | `.mp3`, `.mp4`, `.jpg` | Audio, video, image data |
| System | `.sys`, `.tmp` | OS-related files |

---

**UNIX File Type Classification:**
Unix classifies files into three broad types:
- **Regular files** — contain user data (text or binary)
- **Directory files** — contain list of filenames and their inodes
- **Special files** — represent devices (character/block devices)

---

### 2. Exam Question

> **"What are file types? Explain different types of files supported by an operating system with examples."** *(5 marks)*

---

### 3. Model Answer

**Introduction:**
File type refers to the category of a file based on the nature of its contents and its intended use. The operating system uses file types to determine how to process, execute, or display a file.

**Methods to Indicate File Type:**
- **File Extension** — `.exe`, `.txt`, `.mp3` appended to filename
- **Magic Number** — special byte sequence at the start of the file (Unix/Linux)
- **File Attribute** — stored separately in file metadata

**Types of Files:**

**1. Executable Files**
- Contain machine code ready to be loaded and executed by the CPU.
- Example: `.exe` (Windows), `.out` (Linux)

**2. Object Files**
- Output of a compiler before linking.
- Contain machine code but are not directly executable.
- Example: `.obj`, `.o`

**3. Source Code Files**
- Human-readable programs written in a programming language.
- Example: `.c`, `.java`, `.py`

**4. Text Files**
- Contain plain ASCII or Unicode text.
- Example: `.txt`, `.csv`

**5. Library Files**
- Contain reusable code routines linked by other programs.
- Example: `.dll` (Windows), `.so` (Linux)

**6. Archive / Backup Files**
- Grouped or compressed collections of files.
- Example: `.zip`, `.tar`, `.gz`

**UNIX Classification:**
```
Files in UNIX
├── Regular Files     → text, binary data
├── Directory Files   → list of file names + inodes
└── Special Files     → device files (character/block)
```

**Conclusion:**
File types allow the OS and applications to correctly interpret and handle file contents. Proper type identification ensures files are executed, displayed, or processed appropriately, contributing to system integrity and usability.

---

### 💡 Exam Tips
- Mention **both methods** of type identification — extension AND magic number — for full marks.
- The **UNIX 3-type classification** (regular, directory, special) is a very common exam sub-question.
- Draw the **tree diagram** for UNIX classification — simple but effective.
- Remember: `.dll` = Dynamic Link Library, `.so` = Shared Object — both are library files on different OSes.

## ⚙️ Topic 4: File Operations

### 1. Concept Explanation

**File operations** are the set of **system calls** provided by the OS that allow users and programs to **create, manipulate, and manage files**. The OS kernel handles these operations on behalf of the user process.

The OS maintains two key tables for file operations:
- **Open File Table (System-wide)** — tracks all currently opened files
- **Per-Process File Table** — tracks files opened by each individual process

---

**Basic File Operations:**

### 🔹 1. Create
- Allocate space on disk for the file.
- Create an entry in the directory with file attributes.
- No data is written yet.

### 🔹 2. Write
- A **write pointer** tracks the current position.
- Data is written at the write pointer location.
- Pointer advances after each write.

### 🔹 3. Read
- A **read pointer** tracks the current position.
- Data is read from the current read pointer position.
- Pointer advances after each read.
- *(In most OSes, read and write share a single **current-file-position pointer**)*

### 🔹 4. Reposition (Seek)
- Moves the file pointer to a specified position.
- No actual I/O takes place — just pointer repositioning.
- Also called **file seek**.

### 🔹 5. Delete
- Search directory for the file entry.
- Release all disk space allocated to the file.
- Remove directory entry.

### 🔹 6. Truncate
- Erases the **contents** of the file but **keeps the attributes**.
- File length is reset to zero.
- Disk space is released but the file entry remains.

---

**Open File Table — Key Info Maintained:**

| Information | Purpose |
|---|---|
| File Pointer | Current read/write position |
| File Open Count | Number of processes using the file |
| Disk Location | Pointer to actual data on disk |
| Access Rights | Per-process permissions (read/write/execute) |

---

**Open & Close Operations:**

- **Open(file)** — searches directory, loads file info into open file table, returns a **file descriptor (fd)**
- **Close(file)** — decrements open count; when count = 0, removes entry from open file table

```
Process calls open("file.txt")
        ↓
OS searches directory
        ↓
Loads file info → Open File Table
        ↓
Returns file descriptor (fd = 3)
        ↓
Process uses fd for read/write
        ↓
Process calls close(fd)
```

---

### 2. Exam Question

> **"Explain the basic file operations supported by an operating system. Also explain the role of the open file table."** *(8 marks)*

---

### 3. Model Answer

**Introduction:**
File operations are system calls provided by the OS to perform actions on files. The OS manages these operations using internal data structures like the open file table, ensuring safe and efficient file access.

**Basic File Operations:**

**1. Create**
- A new file is created with no data.
- OS allocates disk space and creates a directory entry with attributes.

**2. Write**
- System call specifies file descriptor and data to write.
- OS uses a write pointer to determine where to write.
- Pointer advances after each write operation.

**3. Read**
- System call specifies file descriptor and buffer to read into.
- OS uses read pointer to fetch data from current position.
- Pointer advances after reading.

**4. Reposition / Seek**
- Repositions the current file pointer to a given offset.
- No actual data transfer — only pointer movement.

**5. Delete**
- OS searches the directory for the file.
- Releases all allocated disk blocks.
- Removes the directory entry completely.

**6. Truncate**
- Resets file length to zero.
- Releases disk space but preserves file attributes (name, permissions).

**Open File Table:**
When a file is opened, the OS creates an entry in the **open file table** containing:
- **File Pointer** — tracks current read/write position
- **File Open Count** — number of processes with file open
- **Disk Location** — physical address of file data
- **Access Rights** — permissions for the opening process

```
Per-Process Table          System-Wide Open File Table
┌──────────────┐           ┌─────────────────────────────┐
│ fd=0 (stdin) │──────────▶│ File Pointer | Location | .. │
│ fd=1 (stdout)│──────────▶│ File Pointer | Location | .. │
│ fd=3 (file)  │──────────▶│ File Pointer | Location | .. │
└──────────────┘           └─────────────────────────────┘
```

**Conclusion:**
File operations form the backbone of file management in an OS. The open file table acts as a bridge between user processes and physical disk storage, enabling efficient, concurrent, and secure file access.

---

### 💡 Exam Tips
- For 8-mark questions, **explain all 6 operations** with at least 2 lines each.
- The **open file table diagram** linking per-process table to system-wide table is highly impressive.
- Key difference to remember: **Delete** removes the file entirely; **Truncate** keeps the file but clears contents.
- Always mention **file descriptor (fd)** when explaining open/close — it's a mark-fetching keyword.
- Examiners often ask **"difference between delete and truncate"** as a sub-question — be ready!

## 📂 Topic 5: Directory Structure

### 1. Concept Explanation

A **directory** is a special file that contains a list of filenames and their attributes (or pointers to them). Directory structures define how files are **organized, named, and accessed** within a file system.

**Goals of a Directory Structure:**
- Efficient file location
- Convenient naming for users
- Grouping of related files
- Support for multiple users

---

**Types of Directory Structures:**

### 🔹 1. Single-Level Directory
- **One directory** for all users and all files.
- Simplest structure.
- **Problem:** No two files can have the same name (naming conflict), even for different users.

```
Directory
┌────────────────────────────────┐
│ file1 | file2 | file3 | file4  │
└────────────────────────────────┘
```
- ✅ Simple to implement
- ❌ No grouping, naming conflicts in multi-user systems

---

### 🔹 2. Two-Level Directory
- Each user has their **own separate directory** (User File Directory - UFD).
- A **Master File Directory (MFD)** maps usernames to their UFDs.

```
MFD
├── User1 → UFD1 [file1, file2]
├── User2 → UFD2 [file1, file3]
└── User3 → UFD3 [fileA, fileB]
```
- ✅ Solves naming conflicts (same name allowed for different users)
- ❌ No grouping within a user's files, no file sharing between users

---

### 🔹 3. Tree-Structured Directory ⭐ (Most Common)
- Generalization of two-level directory into a **tree of arbitrary depth**.
- Each directory can contain files **and subdirectories**.
- **Absolute path:** from root → `/home/user1/docs/file.txt`
- **Relative path:** from current directory → `docs/file.txt`
- **Current Directory (Working Directory)** concept used.

```
Root /
├── home/
│   ├── user1/
│   │   ├── docs/
│   │   └── photos/
│   └── user2/
├── etc/
└── bin/
```
- ✅ Efficient, supports grouping, widely used (Linux, Windows)
- ❌ Difficult to share files between directories

---

### 🔹 4. Acyclic Graph Directory
- Extension of tree structure that allows **shared subdirectories and files**.
- A file/directory can have **multiple parent directories** (shared via links).
- **Hard Link** — two directory entries point to the same inode.
- **Soft Link (Symbolic Link)** — a pointer/shortcut to another file path.
- Must ensure **no cycles** are created.

```
Dir A          Dir B
  └── shared_file ──┘
       (same file, two parents)
```
- ✅ Allows file sharing without duplication
- ❌ Dangling pointers possible if original file is deleted

---

### 🔹 5. General Graph Directory
- Allows **cycles** in the directory structure (unlike acyclic graph).
- Most flexible but requires **garbage collection** to handle cycles.
- Traversal algorithms must handle cycles to avoid infinite loops.
- Rarely used in practice due to complexity.

```
Dir A → Dir B → Dir C → Dir A (cycle!)
```
- ✅ Maximum flexibility
- ❌ Complex, risk of infinite loops, needs cycle detection

---

### 2. Exam Question

> **"Explain the different types of directory structures used in operating systems with neat diagrams."** *(8 marks)*

---

### 3. Model Answer

**Introduction:**
A directory is a file system structure that stores information about files, including their names and locations. Directory structures define how files are organized within a storage system to support efficient access, naming, and sharing.

**1. Single-Level Directory:**
- All files stored in one common directory for all users.
- Simple but causes **naming conflicts** in multi-user environments.

**2. Two-Level Directory:**
- Separate **User File Directory (UFD)** for each user.
- **Master File Directory (MFD)** maps users to their UFDs.
- Solves naming conflicts but lacks file grouping.

**3. Tree-Structured Directory:**
- Most widely used structure (Linux, Windows).
- Directories organized as a tree with a single **root**.
- Supports **absolute** and **relative** paths.
- Enables efficient grouping of related files.

**4. Acyclic Graph Directory:**
- Allows **shared files and subdirectories** using links.
- **Hard Link** — multiple directory entries pointing to the same inode.
- **Symbolic Link** — a shortcut/pointer to another path.
- Cycles are **not allowed** — ensured by the OS.

**5. General Graph Directory:**
- Full generalization allowing cycles.
- Requires **garbage collection** for memory management.
- Needs cycle-detection algorithms during traversal.

**Comparison Table:**

| Structure | Sharing | Grouping | Complexity | Example |
|---|---|---|---|---|
| Single-Level | ❌ | ❌ | Very Low | Early OSes |
| Two-Level | Limited | ❌ | Low | Early Unix |
| Tree | ❌ | ✅ | Medium | Linux, Windows |
| Acyclic Graph | ✅ | ✅ | High | Modern Unix |
| General Graph | ✅ | ✅ | Very High | Rarely used |

**Conclusion:**
Directory structures evolve from simple single-level designs to complex graph-based systems to meet the growing needs of multi-user, multi-tasking environments. Tree-structured directories remain the most practical and widely implemented design.

---

### 💡 Exam Tips
- **Tree-structured directory** is the most important — always explain it in detail with a diagram.
- Remember: **Acyclic Graph ≠ General Graph** — key difference is **cycles are not allowed** in acyclic.
- Keywords to use: **MFD, UFD, inode, hard link, symbolic link, absolute path, relative path**.
- The **comparison table** at the end is a great way to summarize and fetch extra marks.
- Common trick question: *"What is the problem with general graph directories?"* → Answer: **cycles and need for garbage collection**.

---

## 📂 Operations on a Directory

Directory operations are often asked as a **short sub-question (2–3 marks)** or as part of a larger directory question.

---

### Directory Operations:

| Operation | Description |
|---|---|
| **Search** | Find a specific file within the directory by name |
| **Create File** | Add a new file entry into the directory |
| **Delete File** | Remove a file entry from the directory |
| **List Directory** | Display all files and subdirectories in the directory |
| **Rename** | Change the name of a file within the directory |
| **Traverse** | Access every directory and file in the file system (used for backup, search) |
| **Create Directory** | Add a new subdirectory inside an existing directory |
| **Delete Directory** | Remove a subdirectory (usually must be empty first) |

---

### Quick Exam Answer (2–3 marks):

> The following operations can be performed on a directory:
> - **Search** — locate a file by name
> - **Create/Delete File** — add or remove file entries
> - **List** — display contents of a directory
> - **Rename** — change a file's name
> - **Traverse** — walk through the entire directory tree
> - **Create/Delete Directory** — manage subdirectories

---

### 💡 Exam Tip:
- If asked for **"operations on a directory"**, list **at least 5–6** with one-line descriptions.
- **Traverse** is often forgotten by students — mentioning it shows extra knowledge.
- Don't confuse **file operations** (read, write, seek) with **directory operations** (list, search, traverse).

## 🏗️ Topic 6: File System Structure

### 1. Concept Explanation

The **file system structure** defines how a file system is **organized in layers**, where each layer uses the services of the layer below it and provides services to the layer above it. It also describes the **on-disk and in-memory structures** used to manage files.

---

### 🔹 Layered File System Structure

```
┌─────────────────────────────────┐
│       Application Programs      │  ← User level
├─────────────────────────────────┤
│       Logical File System       │  ← Manages metadata, directories
├─────────────────────────────────┤
│       File Organization Module  │  ← Maps logical to physical blocks
├─────────────────────────────────┤
│       Basic File System         │  ← Issues read/write commands to driver
├─────────────────────────────────┤
│       I/O Control (Drivers)     │  ← Device drivers, interrupt handlers
├─────────────────────────────────┤
│          Devices (Disk)         │  ← Physical storage
└─────────────────────────────────┘
```

**Each Layer Explained:**

| Layer | Role |
|---|---|
| **Application Programs** | User programs that use file system via system calls |
| **Logical File System** | Manages metadata (FCB/inode), directory structure, protection |
| **File Organization Module** | Translates logical block addresses to physical block addresses |
| **Basic File System** | Issues generic read/write commands to device drivers |
| **I/O Control** | Device drivers that communicate directly with hardware |
| **Devices** | Actual physical storage (HDD, SSD) |

---

### 🔹 On-Disk Structures

These structures are stored **permanently on disk**:

| Structure | Description |
|---|---|
| **Boot Control Block** | Contains info to boot OS from that volume (boot block in Unix, partition boot sector in NTFS) |
| **Volume Control Block** | Contains volume details — number of blocks, block size, free block count (superblock in Unix, master file table in NTFS) |
| **Directory Structure** | Organizes files — contains file names and inode numbers |
| **File Control Block (FCB)** | Contains all details about a file — permissions, size, location of data blocks (inode in Unix) |

---

### 🔹 In-Memory Structures

These structures are maintained **in RAM** for performance:

| Structure | Description |
|---|---|
| **Mount Table** | Info about each mounted volume |
| **Directory Cache** | Recently accessed directory info for fast lookup |
| **System-Wide Open File Table** | Info about all currently open files |
| **Per-Process Open File Table** | File descriptors for each process |
| **Buffer Cache** | Disk block cache to reduce I/O operations |

---

### 🔹 File Control Block (FCB) / Inode

The **FCB (inode in Unix)** is the most important file system structure — it stores all metadata about a file:

```
FCB / Inode
┌──────────────────────┐
│ File Permissions     │
│ File Dates (created, │
│  modified, accessed) │
│ File Owner, Group    │
│ File Size            │
│ Pointers to data     │
│ blocks on disk       │
└──────────────────────┘
```

---

### 2. Exam Question

> **"Explain the layered file system structure. Also describe on-disk and in-memory structures used in file systems."** *(8 marks)*

---

### 3. Model Answer

**Introduction:**
A file system is organized as a **layered structure**, where each layer builds upon the services of the layer below. This modular design simplifies implementation and improves maintainability. The file system uses both on-disk and in-memory data structures for efficient file management.

**Layered Structure:**

**1. I/O Control Layer:**
- Consists of device drivers and interrupt handlers.
- Transfers data between memory and disk hardware.

**2. Basic File System:**
- Issues generic commands (read block X, write block Y) to device drivers.
- Manages memory buffers and caches.

**3. File Organization Module:**
- Knows about files and their logical and physical blocks.
- Translates logical block numbers to physical disk addresses.

**4. Logical File System:**
- Manages all file metadata via **File Control Blocks (FCBs)**.
- Handles directory structures and file protection.

**On-Disk Structures:**
- **Boot Control Block** — boot information for the volume
- **Volume Control Block** — volume details (block count, free blocks)
- **Directory Structure** — file names mapped to FCBs
- **FCB (inode)** — complete metadata of each file

**In-Memory Structures:**
- **Mount Table** — tracks mounted file systems
- **Directory Cache** — speeds up directory lookups
- **Open File Tables** — system-wide and per-process
- **Buffer Cache** — reduces disk I/O by caching blocks

**Conclusion:**
The layered file system structure promotes modularity and abstraction. On-disk structures ensure data persistence while in-memory structures boost performance, together forming a complete and efficient file management system.

---

### 💡 Exam Tips
- The **layered diagram** is essential — draw it as a stack with arrows showing top-to-bottom flow.
- Remember: **FCB = inode** in Unix — examiners may use either term.
- Key distinction: **On-disk = permanent storage**, **In-memory = temporary, for speed**.
- **Superblock** (Unix) = Volume Control Block — mention both terms.
- For 8-mark questions, cover **all 4 layers + on-disk + in-memory structures** for full marks.

## 💾 Topic 7: Allocation Methods

### 1. Concept Explanation

**Allocation methods** define how **disk space is allocated to files**. The goal is to utilize disk space efficiently and allow fast access to files.

There are **three main allocation methods:**

---

### 🔹 1. Contiguous Allocation

Each file occupies a **set of contiguous (adjacent) blocks** on disk.

**Directory entry stores:** Starting block + Length (number of blocks)

```
Block:  0    1    2    3    4    5    6    7    8
      ┌────┬────┬────┬────┬────┬────┬────┬────┬────┐
      │    │ F1 │ F1 │ F1 │    │ F2 │ F2 │    │    │
      └────┴────┴────┴────┴────┴────┴────┴────┴────┘
        F1 starts at 1, length 3
        F2 starts at 5, length 2
```

**Advantages:**
- ✅ Simple implementation
- ✅ Excellent **sequential access** performance
- ✅ Supports **direct access** easily (start + offset)

**Disadvantages:**
- ❌ **External fragmentation** — free space scattered in small chunks
- ❌ File size must be known in advance
- ❌ Difficult to grow files dynamically

---

### 🔹 2. Linked Allocation

Each file is a **linked list of disk blocks** scattered anywhere on disk. Each block contains a **pointer to the next block**.

**Directory entry stores:** Starting block + Ending block

```
Directory → Block 2 → Block 5 → Block 8 → Block 1 → NULL
              ↓          ↓          ↓          ↓
           [data|•]  [data|•]  [data|•]  [data|/]
```

**Advantages:**
- ✅ **No external fragmentation**
- ✅ Files can grow dynamically
- ✅ No need to declare file size in advance

**Disadvantages:**
- ❌ **No direct access** — must traverse from start (sequential only)
- ❌ Pointer overhead wastes space in each block
- ❌ **Reliability issue** — if one pointer is corrupted, entire file is lost

**FAT (File Allocation Table):**
- Variation of linked allocation.
- All pointers stored in a **FAT table** at the beginning of the disk.
- Allows direct access by traversing FAT (in memory) instead of disk.

```
FAT Table         Disk Blocks
┌───┬───┐        ┌──────────┐
│ 2 │ 5 │───────▶│  Block 2 │
│ 5 │ 8 │───────▶│  Block 5 │
│ 8 │ 1 │───────▶│  Block 8 │
│ 1 │EOF│───────▶│  Block 1 │
└───┴───┘        └──────────┘
```

---

### 🔹 3. Indexed Allocation ⭐

Each file has its own **index block** — a special block containing **pointers to all data blocks** of the file.

**Directory entry stores:** Pointer to index block

```
Directory → Index Block
               ┌─────────┐
               │ ptr → 3 │──→ Block 3 (data)
               │ ptr → 7 │──→ Block 7 (data)
               │ ptr → 1 │──→ Block 1 (data)
               │ ptr → 9 │──→ Block 9 (data)
               └─────────┘
```

**Advantages:**
- ✅ Supports **direct access** efficiently
- ✅ **No external fragmentation**
- ✅ Files can grow dynamically

**Disadvantages:**
- ❌ Index block wastes space for small files
- ❌ Large files may need **multiple index blocks**

---

**Handling Large Files — Multi-level Indexing:**

### Linked Scheme:
Index blocks linked together for large files.

### Multilevel Index:
- **1st level** index block points to **2nd level** index blocks.
- 2nd level index blocks point to actual data blocks.

### Combined Scheme (Unix inode): ⭐
Used in Unix/Linux — most important for exams!

```
inode
┌─────────────────────┐
│ 12 Direct Pointers  │──→ Data Blocks (small files)
├─────────────────────┤
│ 1 Single Indirect   │──→ Index Block → Data Blocks
├─────────────────────┤
│ 1 Double Indirect   │──→ Index Block → Index Block → Data
├─────────────────────┤
│ 1 Triple Indirect   │──→ Index → Index → Index → Data
└─────────────────────┘
```

---

### Comparison Table:

| Feature | Contiguous | Linked | Indexed |
|---|---|---|---|
| Access Type | Sequential + Direct | Sequential only | Sequential + Direct |
| External Fragmentation | Yes | No | No |
| Internal Fragmentation | No | No | Yes (index block) |
| File Growth | Difficult | Easy | Easy |
| Pointer Overhead | None | Per block | Index block |
| Reliability | High | Low | High |
| Used In | CD-ROMs | FAT (Windows) | Unix/Linux (inode) |

---

### 2. Exam Question

> **"Explain the three file allocation methods with diagrams. Compare their advantages and disadvantages."** *(8 marks)*

---

### 3. Model Answer

**Introduction:**
File allocation methods determine how disk blocks are assigned to files. The OS must choose an allocation strategy that minimizes fragmentation, supports efficient access, and allows dynamic file growth.

**1. Contiguous Allocation:**
- Each file stored in consecutive disk blocks.
- Directory stores start block and length.
- Supports both sequential and direct access.
- Suffers from **external fragmentation** and inflexibility in file growth.

**2. Linked Allocation:**
- File stored as a linked list of scattered blocks.
- Each block holds a pointer to the next block.
- No fragmentation but **no direct access**.
- FAT is an improvement — stores all pointers in a table in memory.

**3. Indexed Allocation:**
- Each file has a dedicated **index block** holding pointers to data blocks.
- Supports direct access with no external fragmentation.
- For large files, **multilevel or combined indexing** (Unix inode) is used:
  - **Direct pointers** — for small files
  - **Single indirect** — index block → data
  - **Double indirect** — index → index → data
  - **Triple indirect** — index → index → index → data

**Conclusion:**
Each allocation method has trade-offs. Contiguous allocation offers speed, linked allocation offers flexibility, and indexed allocation balances both. Unix uses a **combined inode scheme** to efficiently handle files of all sizes.

---

### 💡 Exam Tips
- This is one of the **most frequently asked topics** in OS exams — master all three methods.
- Always draw the **inode combined scheme diagram** — it fetches maximum marks.
- Remember: **FAT = linked allocation improvement** — mention it for extra marks.
- Key terms: **external fragmentation, direct access, index block, inode, FAT**.
- Trick question: *"Which method is used in Unix?"* → **Indexed allocation with combined inode scheme**.
- For contiguous: *"What are the two problems?"* → **External fragmentation + file size must be known**.

## 🆓 Topic 8: Free Space Management

### 1. Concept Explanation

**Free space management** refers to how the OS **tracks and manages unallocated (free) disk blocks** so that they can be assigned to new files when needed.

The OS maintains a **free-space list** — a data structure that records all free disk blocks.

There are **three main methods:**

---

### 🔹 1. Bit Vector (Bitmap)

The free-space list is implemented as a **bitmap/bit vector** where each bit represents one disk block:
- **Bit = 1** → Block is **free**
- **Bit = 0** → Block is **allocated**

```
Block No:  0    1    2    3    4    5    6    7    8    9
Bit:      [0]  [0]  [1]  [1]  [0]  [1]  [0]  [0]  [1]  [1]
                ↑         ↑         ↑              ↑    ↑
              free       free      free           free free
```

**Finding free block:**
- Scan bit vector for first bit = 1
- Formula to find block number:
```
Block number = (word size × number of 0-value words) + offset of first 1 bit
```

**Advantages:**
- ✅ Simple to implement
- ✅ Efficient to find **first free block** or **n consecutive free blocks**
- ✅ Easy to get contiguous blocks

**Disadvantages:**
- ❌ Bit vector must be kept **in memory** for efficiency — consumes RAM
- ❌ Not efficient for large disks (large bitmap size)

---

### 🔹 2. Linked List (Free List)

All free disk blocks are **linked together** using pointers — each free block contains the address of the next free block.

A special pointer (stored in memory) points to the **first free block**.

```
Free List Head
     ↓
  Block 2 → Block 5 → Block 8 → Block 11 → NULL
  [  |•]    [  |•]    [  |•]    [  |/]
```

**Advantages:**
- ✅ No waste of space — pointers stored inside free blocks themselves
- ✅ Simple to allocate one block (take head of list)

**Disadvantages:**
- ❌ **Inefficient** — cannot easily find contiguous blocks
- ❌ Traversing the list requires many disk I/O operations
- ❌ Not suitable for contiguous allocation

---

### 🔹 3. Grouping

A **modification of linked list** to improve efficiency.

- First free block stores addresses of **n free blocks**.
- The **last of those n blocks** stores addresses of another n free blocks.
- And so on...

```
Block 2 (stores addresses):
┌─────────────────────────────────────┐
│ Block 5 | Block 8 | Block 11 | →16  │
└─────────────────────────────────────┘
                                  ↓
                          Block 16 (stores next group):
                          ┌──────────────────────────┐
                          │ Block 20 | Block 23 | →30 │
                          └──────────────────────────┘
```

**Advantages:**
- ✅ Large number of free blocks found **quickly**
- ✅ More efficient than simple linked list

**Disadvantages:**
- ❌ More complex than simple linked list
- ❌ Still not as fast as bit vector for contiguous allocation

---

### 🔹 4. Counting (Bonus Method)

- Instead of storing individual free blocks, stores **(start block, count)** pairs.
- Takes advantage of the fact that **contiguous blocks are often free/allocated together**.

```
Free List:
┌──────────────────────────────────────┐
│ (Block 2, 3) | (Block 8, 2) | ...    │
└──────────────────────────────────────┘
  ↑                ↑
  Blocks 2,3,4    Blocks 8,9
  are free        are free
```

**Advantages:**
- ✅ Compact representation — fewer entries needed
- ✅ Efficient for contiguous allocation

**Disadvantages:**
- ❌ More complex to implement and maintain

---

### Comparison Table:

| Method | Storage | Speed | Contiguous Blocks | Best Used When |
|---|---|---|---|---|
| Bit Vector | Bitmap in memory | Fast | Easy to find | Small to medium disks |
| Linked List | Inside free blocks | Slow | Hard to find | Simple systems |
| Grouping | Grouped blocks | Moderate | Moderate | Large free spaces |
| Counting | (start, count) pairs | Fast | Very easy | Contiguous allocation |

---

### 2. Exam Question

> **"Explain the different methods of free space management in an operating system with diagrams."** *(8 marks)*

---

### 3. Model Answer

**Introduction:**
Free space management is the mechanism by which the OS tracks unallocated disk blocks to efficiently assign them to new files. The OS maintains a **free-space list** using various data structures, each with different trade-offs in speed, complexity, and space usage.

**1. Bit Vector (Bitmap):**
- Each disk block is represented by one bit.
- Bit = 1 means free, Bit = 0 means allocated.
- OS scans the bitmap to find free blocks.
- Efficient for finding contiguous free blocks.
- Entire bitmap kept in memory for fast access.

**2. Linked List:**
- All free blocks linked together via pointers stored inside free blocks.
- Head pointer (in memory) points to first free block.
- Allocating a block = removing head of list.
- Simple but **inefficient** for finding contiguous blocks.

**3. Grouping:**
- Modification of linked list for efficiency.
- First free block stores addresses of n free blocks.
- Last entry in each group points to next group of free blocks.
- Allows **large number of free blocks** to be found quickly.

**4. Counting:**
- Stores **(start block, count)** pairs instead of individual blocks.
- Exploits the fact that contiguous blocks tend to be freed together.
- Compact and efficient for contiguous allocation systems.

**Diagrams:**
```
Bit Vector:
[0][0][1][1][0][1][0][1]  → Blocks 2,3,5,7 are free

Linked List:
Head → [B2|→] → [B5|→] → [B8|/]

Grouping:
[B2: B5,B8,B11,→B16] → [B16: B20,B23,→B30]

Counting:
[(B2,3), (B8,2), (B15,4)]
```

**Conclusion:**
Free space management is critical for disk efficiency. Bit vectors are fastest for small disks, linked lists are simplest, grouping improves linked list performance, and counting is best for contiguous allocation systems. Modern OSes like Linux use **bitmaps** for their efficiency.

---

### 💡 Exam Tips
- Syllabus specifically mentions **bit vector, linked list, and grouping** — cover all three in detail.
- Always mention **bit = 1 means free, bit = 0 means allocated** — commonly confused.
- **Grouping** is often confused with linked list — clarify that grouping stores **multiple addresses per block**.
- Linux uses **bitmap** for free space management — mention this for extra marks.
- Counting method may not be in syllabus explicitly but mentioning it shows depth of knowledge.
- Common exam question: *"Compare bit vector and linked list for free space management"* — be ready!

## 📋 Topic 9: Directory Implementation

### 1. Concept Explanation

**Directory implementation** refers to the **data structures used internally** by the OS to store and manage directory entries. The choice of data structure affects the **speed of file lookup, insertion, and deletion**.

There are **two main methods:**

---

### 🔹 1. Linear List

The directory is implemented as a **simple linear list** of file entries, where each entry contains:
- File name
- Pointer to data blocks (or FCB/inode number)
- File attributes

```
Directory (Linear List)
┌──────────────────────────────────────┐
│ Entry 1: file1.txt | inode=23 | ...  │
│ Entry 2: file2.c   | inode=47 | ...  │
│ Entry 3: file3.exe | inode=12 | ...  │
│ Entry 4: file4.mp3 | inode=89 | ...  │
└──────────────────────────────────────┘
```

**How Operations Work:**

| Operation | Process |
|---|---|
| **Search** | Linear scan from beginning until name matched |
| **Create** | Search entire list (check no duplicate), add at end |
| **Delete** | Search for entry, mark as unused or shift entries |

**Advantages:**
- ✅ Simple to implement
- ✅ Easy to create and delete entries

**Disadvantages:**
- ❌ **Linear search is slow** — O(n) time complexity
- ❌ Performance degrades as directory grows larger
- ❌ Finding a file requires scanning all entries

**Improvement — Sorted List:**
- Keep entries sorted alphabetically.
- Allows **binary search** → O(log n) lookup.
- But insertion and deletion become more complex.

---

### 🔹 2. Hash Table

The directory is implemented as a **hash table** where:
- File name is passed through a **hash function**.
- Hash function returns an index into the hash table.
- Each index points to the actual directory entry.

```
File Name          Hash Function       Hash Table
"file1.txt"   →   h("file1.txt") = 3  →  [3] → Entry(file1.txt, inode=23)
"file2.c"     →   h("file2.c")   = 7  →  [7] → Entry(file2.c, inode=47)
"file3.exe"   →   h("file3.exe") = 1  →  [1] → Entry(file3.exe, inode=12)

Hash Table:
┌───┬──────────────────────────┐
│ 0 │ NULL                     │
│ 1 │ → file3.exe | inode=12   │
│ 2 │ NULL                     │
│ 3 │ → file1.txt | inode=23   │
│ 4 │ NULL                     │
│ 5 │ NULL                     │
│ 6 │ NULL                     │
│ 7 │ → file2.c   | inode=47   │
└───┴──────────────────────────┘
```

**How Operations Work:**

| Operation | Process |
|---|---|
| **Search** | Hash filename → go directly to index → O(1) lookup |
| **Create** | Hash filename → insert at index → check for collision |
| **Delete** | Hash filename → go to index → remove entry |

**Collision Handling:**
When two filenames hash to the same index:
- **Chaining** — maintain a linked list at each hash index
- **Open Addressing** — probe next available slot

```
Collision Example (Chaining):
[3] → file1.txt → fileX.txt → NULL
       (both hashed to index 3)
```

**Advantages:**
- ✅ **Very fast lookup** — O(1) average time
- ✅ Insertion and deletion also fast
- ✅ Scales well for large directories

**Disadvantages:**
- ❌ **Hash collisions** must be handled
- ❌ Fixed hash table size — may need **rehashing** when full
- ❌ More complex to implement than linear list

---

### Comparison Table:

| Feature | Linear List | Hash Table |
|---|---|---|
| Search Time | O(n) | O(1) average |
| Implementation | Simple | Complex |
| Collision Issue | No | Yes |
| Sorted Access | Possible | Difficult |
| Space Usage | Low | Moderate |
| Best For | Small directories | Large directories |

---

### 2. Exam Question

> **"Explain the two methods of directory implementation — Linear List and Hash Table — with their advantages and disadvantages."** *(6 marks)*

---

### 3. Model Answer

**Introduction:**
Directory implementation refers to the internal data structures used by the OS to manage directory entries. The two primary methods are **linear list** and **hash table**, each offering different trade-offs between simplicity and performance.

**1. Linear List:**
- Directory stored as a sequential list of (filename, inode/FCB pointer) entries.
- **Search** requires scanning from the first entry until a match is found — O(n).
- **Create** — scan entire list to check for duplicates, then append new entry.
- **Delete** — find entry and mark it free or compact the list.
- Simple but **slow for large directories**.
- Improvement: **sorted list + binary search** reduces lookup to O(log n).

**2. Hash Table:**
- A hash function maps each filename to an index in a fixed-size table.
- **Search** — hash the filename, go directly to the index — O(1) average.
- **Create** — hash filename, insert at computed index.
- **Delete** — hash filename, remove entry at index.
- **Collision** occurs when two filenames hash to the same index:
  - Resolved by **chaining** (linked list at each index) or **open addressing**.
- Fast and scalable but requires **collision handling** and **rehashing**.

**Diagram:**
```
Linear List:               Hash Table:
┌────────────────┐         [0] → NULL
│ file1 | inode1 │         [1] → file3 | inode3
│ file2 | inode2 │         [2] → NULL
│ file3 | inode3 │         [3] → file1 | inode1
└────────────────┘         [4] → file2 | inode2
  Search: O(n)               Search: O(1)
```

**Conclusion:**
Linear list is suitable for small directories due to its simplicity, while hash tables are preferred for large directories due to their O(1) lookup efficiency. Most modern file systems use hash tables or B-trees for directory implementation.

---

### 💡 Exam Tips
- Always mention **time complexities** — O(n) for linear, O(1) for hash — examiners love this.
- **Collision** is the key disadvantage of hash table — always explain it with chaining.
- Mention **sorted linear list + binary search** as an improvement — shows deeper understanding.
- Modern file systems like **ext4 (Linux)** use **HTree (Hash Tree)** — mention for extra marks.
- Common sub-question: *"What is a hash collision and how is it resolved?"*
  - Answer: **Chaining or Open Addressing**

## 🐧 Topic 10: Linux File System

### 1. Concept Explanation

The **Linux File System** is based on the **Unix File System (UFS)** model and uses the **Extended File System** family. The most widely used is **ext4** (Fourth Extended File System).

Linux follows the **"everything is a file"** philosophy — devices, pipes, sockets are all represented as files.

---

### 🔹 Virtual File System (VFS)

Linux uses a **VFS layer** that provides a **uniform interface** between the OS and different file system types.

```
User Process (open, read, write system calls)
            ↓
    Virtual File System (VFS)
            ↓
  ┌─────────┬──────────┬─────────┐
  │  ext4   │   FAT   │  NFS    │  ← Actual File Systems
  └─────────┴──────────┴─────────┘
            ↓
        Physical Storage
```

**Benefits of VFS:**
- Applications don't need to know which file system is being used.
- Multiple file systems can be mounted simultaneously.
- Uniform system calls (open, read, write) work across all file systems.

---

### 🔹 Linux Directory Structure

Linux follows a **single-rooted hierarchical tree** structure:

```
/ (root)
├── bin/    → Essential user binaries (ls, cp)
├── boot/   → Boot loader files, kernel
├── dev/    → Device files
├── etc/    → System configuration files
├── home/   → User home directories
├── lib/    → Shared libraries
├── mnt/    → Mount points
├── proc/   → Virtual filesystem (process info)
├── root/   → Root user home directory
├── tmp/    → Temporary files
├── usr/    → User programs and utilities
└── var/    → Variable data (logs, spools)
```

---

### 🔹 ext4 File System ⭐

**ext4** is the default Linux file system. Key features:

| Feature | Description |
|---|---|
| **Journaling** | Records changes in a journal before committing — prevents corruption on crash |
| **Extents** | Contiguous block ranges instead of individual block pointers — reduces fragmentation |
| **Delayed Allocation** | Delays block allocation until data is flushed — improves performance |
| **Large File Support** | Supports files up to **16 TB** and volumes up to **1 EB** |
| **Backward Compatible** | Can mount ext2 and ext3 file systems |

---

### 🔹 inode Structure in Linux ⭐

Every file in Linux has an **inode** (index node) that stores all metadata:

```
inode
┌──────────────────────────┐
│ File type & permissions  │
│ Owner (UID, GID)         │
│ File size                │
│ Timestamps (create,      │
│   modify, access)        │
│ Link count               │
│ 12 Direct block pointers │
│ 1 Single indirect ptr    │
│ 1 Double indirect ptr    │
│ 1 Triple indirect ptr    │
└──────────────────────────┘
```

- **inode does NOT store filename** — filename is stored in the directory entry.
- Directory entry maps **filename → inode number**.

---

### 🔹 Journaling in ext4 ⭐

**Journaling** ensures file system consistency after crashes:

```
Step 1: Write changes to JOURNAL (log)
         ↓
Step 2: Commit journal entry
         ↓
Step 3: Write actual changes to FILE SYSTEM
         ↓
Step 4: Mark journal entry as complete
```

If system crashes:
- Before Step 3 → replay journal to redo changes
- After Step 3 → journal cleared, file system consistent

**Journal Modes:**
| Mode | What is Journaled | Speed |
|---|---|---|
| **Journal** | Data + Metadata | Slowest but safest |
| **Ordered** | Metadata only (default) | Balanced |
| **Writeback** | Metadata only (unordered) | Fastest but less safe |

---

### 2. Exam Question

> **"Describe the Linux file system. Explain the role of VFS, inode structure, and journaling in ext4."** *(8 marks)*

---

### 3. Model Answer

**Introduction:**
Linux uses the **Extended File System (ext)** family, with **ext4** being the current standard. It follows the Unix philosophy of treating everything as a file and uses a **Virtual File System (VFS)** layer to support multiple file system types uniformly.

**Virtual File System (VFS):**
- VFS is an abstraction layer between user processes and actual file systems.
- Provides uniform system calls (open, read, write) regardless of underlying file system.
- Allows simultaneous mounting of ext4, FAT, NFS, and other file systems.

**Linux Directory Structure:**
- Single-rooted tree starting at **/ (root)**.
- Key directories: `/bin` (binaries), `/etc` (config), `/home` (users), `/dev` (devices), `/proc` (process info).

**inode Structure:**
- Every file has an inode storing: permissions, owner, size, timestamps, link count, and **block pointers**.
- Uses **12 direct + 1 single indirect + 1 double indirect + 1 triple indirect** pointers.
- **Filename is NOT stored in inode** — stored in directory entry which maps to inode number.

**ext4 Features:**
- **Journaling** — logs changes before committing, ensuring consistency after crashes.
- **Extents** — stores contiguous block ranges, reducing fragmentation.
- **Delayed Allocation** — improves performance by deferring block allocation.
- Supports files up to **16 TB** and volumes up to **1 EB**.

**Journaling Process:**
```
Write to Journal → Commit → Write to Disk → Clear Journal
```
Three modes: **Journal** (safest), **Ordered** (default), **Writeback** (fastest).

**Conclusion:**
The Linux file system combines VFS flexibility, inode-based metadata management, and ext4 journaling to provide a robust, efficient, and crash-resistant file management system widely used in servers and desktops.

---

### 💡 Exam Tips
- **VFS diagram** showing the abstraction layer is very effective — always draw it.
- Remember: **inode does NOT store filename** — this is a very common trick question!
- **Journaling** is frequently asked separately — know all 3 modes (Journal, Ordered, Writeback).
- ext4 key numbers: **16 TB max file size, 1 EB max volume size** — mention for impressiveness.
- Linux directory structure — memorize at least **6–7 key directories** with their purpose.
- Common exam question: *"What is the role of VFS in Linux?"* — always answer with the diagram.

## 🤖 Topic 11: Android File System

### 1. Concept Explanation

The **Android File System** is built on top of the **Linux kernel** and uses a combination of multiple file systems for different purposes. Android's storage is divided into **internal storage, external storage, and virtual file systems**.

---

### 🔹 File Systems Used in Android

| File System | Purpose |
|---|---|
| **ext4** | Internal storage (apps, data) |
| **F2FS** (Flash-Friendly File System) | Internal storage on flash/SSD — optimized for NAND flash |
| **FAT32 / exFAT** | External storage (SD cards, USB) |
| **tmpfs** | Temporary in-memory file system (RAM-based) |
| **procfs** | Virtual FS exposing process/kernel info (`/proc`) |
| **sysfs** | Virtual FS exposing device/driver info (`/sys`) |

---

### 🔹 Android Directory Structure

Android follows Linux-style directory hierarchy:

```
/ (root)
├── system/     → Core OS files (read-only)
├── data/       → User apps, settings, app data (read-write)
│   ├── app/         → Installed APK files
│   ├── data/        → App-specific private data
│   └── media/       → Internal media storage
├── cache/      → Temporary cached data
├── sdcard/     → External/emulated storage (symlink)
├── proc/       → Process information (virtual)
├── sys/        → System/device info (virtual)
├── dev/        → Device files
└── vendor/     → Hardware-specific vendor files
```

---

### 🔹 Android Storage Architecture ⭐

Android organizes storage into distinct layers:

```
┌─────────────────────────────────────┐
│         Applications (APKs)         │
├─────────────────────────────────────┤
│     Android Storage Framework       │
│  (MediaStore, Storage Access        │
│         Framework - SAF)            │
├─────────────────────────────────────┤
│        Linux VFS Layer              │
├──────────────┬──────────────────────┤
│     ext4     │   F2FS   │  FAT32   │
├──────────────┴──────────────────────┤
│     Physical Storage (Flash/eMMC)   │
└─────────────────────────────────────┘
```

---

### 🔹 F2FS (Flash-Friendly File System) ⭐

Developed by **Samsung**, specifically designed for **NAND flash storage**:

| Feature | Description |
|---|---|
| **Log-structured** | Writes data sequentially to avoid random writes on flash |
| **Wear Leveling** | Distributes writes evenly across flash cells to extend lifespan |
| **No Journaling Overhead** | Uses log structure instead — faster than ext4 on flash |
| **Optimized for Flash** | Reduces write amplification — improves flash longevity |

---

### 🔹 Android Storage Types

**1. Internal Storage:**
- Private to each app — other apps cannot access it.
- Stored under `/data/data/<package_name>/`
- Deleted when app is uninstalled.
- Uses **ext4 or F2FS**.

**2. External Storage:**
- Shared storage accessible by all apps (with permission).
- Physical SD card or **emulated** using internal flash.
- Uses **FAT32 or exFAT**.
- Stored under `/sdcard/` or `/storage/emulated/0/`

**3. Cache Storage:**
- Temporary files stored by apps.
- OS can clear cache when storage is low.
- Stored under `/cache/`

---

### 🔹 Security Features in Android File System

| Feature | Description |
|---|---|
| **App Sandboxing** | Each app has its own private directory — cannot access other app data |
| **SELinux** | Security-Enhanced Linux — enforces mandatory access control policies |
| **Encryption** | Full-disk encryption (FDE) or File-Based Encryption (FBE) |
| **Permissions** | READ/WRITE_EXTERNAL_STORAGE permissions required for shared storage |

---

### 🔹 Key Difference: Linux vs Android File System

| Feature | Linux (ext4) | Android |
|---|---|---|
| Primary FS | ext4 | ext4 + F2FS |
| External Storage | Not standard | FAT32/exFAT (SD card) |
| Security | DAC (permissions) | DAC + SELinux + Sandboxing |
| Storage Types | Single hierarchy | Internal + External + Cache |
| Optimized For | HDD/SSD | Flash/eMMC storage |
| Virtual FS | procfs, sysfs | procfs, sysfs, tmpfs |

---

### 2. Exam Question

> **"Describe the Android file system. Explain the different file systems used, directory structure, and storage types in Android."** *(8 marks)*

---

### 3. Model Answer

**Introduction:**
Android is built on the **Linux kernel** and uses a combination of file systems tailored for mobile devices. Its file system is optimized for **NAND flash storage**, app sandboxing, and multiple storage types including internal, external, and cache storage.

**File Systems Used in Android:**
- **ext4** — Primary file system for internal storage; supports journaling.
- **F2FS** — Flash-Friendly File System developed by Samsung; log-structured, optimized for NAND flash, reduces write amplification.
- **FAT32/exFAT** — Used for external SD cards and USB storage for cross-platform compatibility.
- **tmpfs** — RAM-based temporary file system.
- **procfs/sysfs** — Virtual file systems for process and device information.

**Android Directory Structure:**
- `/system` — Core read-only OS files
- `/data` — User apps and private app data
- `/cache` — Temporary cached data
- `/sdcard` — External or emulated storage
- `/proc`, `/sys`, `/dev` — Virtual and device files

**Storage Types:**
1. **Internal Storage** — Private per-app storage under `/data/data/<package>/`; deleted on uninstall; uses ext4/F2FS.
2. **External Storage** — Shared storage on SD card or emulated flash; uses FAT32/exFAT; requires permissions.
3. **Cache Storage** — Temporary files under `/cache/`; cleared by OS when storage is low.

**Security Features:**
- **App Sandboxing** — Each app isolated in its own directory.
- **SELinux** — Mandatory access control enforcement.
- **Encryption** — File-Based Encryption (FBE) in modern Android.

**Conclusion:**
The Android file system combines Linux's robust VFS architecture with mobile-optimized file systems like F2FS and strict security mechanisms like sandboxing and SELinux, making it efficient, secure, and well-suited for resource-constrained mobile environments.

---

### 💡 Exam Tips
- Key difference to highlight: **F2FS is designed for flash** while **ext4 is designed for HDDs** — this shows understanding.
- Remember **F2FS = Samsung + NAND flash + log-structured** — three keywords for full marks.
- **App sandboxing** is unique to Android — always mention it when comparing with Linux.
- Storage types (Internal, External, Cache) with their **paths** fetch good marks.
- Common exam question: *"How does Android file system differ from Linux file system?"* — use the comparison table.
- **SELinux + FBE (File-Based Encryption)** are modern Android security keywords — use them!

---

## 🎉 Unit 6: File Management — Complete!

Here's a quick recap of all topics covered:

| # | Topic | Key Concept |
|---|---|---|
| 1 | File Concept | Attributes, structure, abstraction |
| 2 | Access Methods | Sequential, Direct, Indexed |
| 3 | File Types | Extensions, magic numbers, UNIX types |
| 4 | File Operations | Create, read, write, delete, truncate |
| 5 | Directory Structure | Single, Two-level, Tree, Acyclic, Graph |
| 6 | File System Structure | Layers, FCB, on-disk, in-memory |
| 7 | Allocation Methods | Contiguous, Linked, Indexed, inode |
| 8 | Free Space Management | Bit vector, Linked list, Grouping |
| 9 | Directory Implementation | Linear list, Hash table |
| 10 | Linux File System | VFS, ext4, inode, journaling |
| 11 | Android File System | F2FS, storage types, sandboxing |

---

## Linux vs Android File System

| # | Feature | Linux File System | Android File System |
|---|---|---|---|
| 1 | **Primary File System** | ext4 | ext4 + F2FS |
| 2 | **Optimized For** | HDD / SSD storage | NAND Flash / eMMC (mobile) |
| 3 | **External Storage** | Not natively supported | FAT32 / exFAT for SD cards & USB |
| 4 | **Security Model** | DAC (Discretionary Access Control) + SELinux | DAC + SELinux + **App Sandboxing** + Encryption |
| 5 | **Storage Types** | Single unified hierarchy | Internal + External + Cache storage |
| 6 | **Virtual File Systems** | procfs, sysfs | procfs, sysfs, tmpfs + Android Storage Framework (SAF) |

---

### 💡 Exam Tip
- This is a common **5–6 mark question** — a clean table with 6 points is the perfect answer.
- Always mention **F2FS and App Sandboxing** — these are the two most unique Android-specific keywords that differentiate it from Linux.
