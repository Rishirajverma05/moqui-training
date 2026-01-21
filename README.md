# Module 0: Getting Started with Moqui

This README documents the complete setup and configuration steps followed to get started with the **Moqui Framework** on a Windows machine, along with IntelliJ IDEA configuration for Moqui development.

***

## Prerequisites

The following prerequisites were completed before starting with Moqui setup:

- **Git Installation**  
  Git was already installed and available on the system.

- **Java Development Kit (JDK 11)**  
  Initially, JDK 25 was installed. For Moqui, JDK 11 was installed and configured by setting the environment variables, then verified using the command:  
  `java -version`  
  The output was:  
  `openjdk version "11.0.0.2" 2024-07-02`

- **GitHub Account**  
  An active GitHub account is available for working with repositories.

All prerequisites required for the Moqui setup were successfully completed.

***

## Step 1: Clone the Moqui Framework

1. Open the terminal on Windows.
2. Create a folder named `sandbox` and navigate into it.
3. Clone the Moqui Framework repository using the following command:
   ```bash
   git clone -b master https://github.com/moqui/moqui-framework.git
   ```

This creates a local copy of the Moqui Framework inside the `sandbox` directory for development.

***

## Step 2: Initialize the Runtime Component

1. Navigate to the `moqui-framework` folder using the `cd` command.
2. Set up the runtime directory using the Gradle wrapper command:
   ```bash
   ./gradlew getRuntime
   ```

Moqui includes Gradle with the project, so a separate Gradle installation was not required.

### Error Encountered: TLS Handshake Failure

While running `./gradlew getRuntime`, the following error occurred:

> FAILURE: Build failed with an exception.  
> Could not resolve all files for configuration ':classpath'.  
> Could not resolve org.slf4j:slf4j-api:1.7.30  
> Received fatal alert: handshake_failure

**Reason for the Error:**

- Gradle was trying to download dependencies from the internet.
- The Java/Windows environment could not establish a secure TLS/SSL connection.
- This was due to a security protocol incompatibility between the system and the remote server.

**Solution Used:**

The issue was resolved by forcing Java/Gradle to use a specific TLS version:

```bash
gradlew getRuntime -Dorg.gradle.jvmargs="-Dhttps.protocols=TLSv1.2"
```

- The `-D` flag passes a system property to the JVM.
- `-Dhttps.protocols=TLSv1.2` forces Java to use the TLSv1.2 protocol, which allows a compatible secure connection for dependency download.

After applying this command, the runtime initialization completed successfully.

***

## Step 3: Run the Moqui Application

### 3.1 Load Data and Start Server

1. Load data (seed, initial, or demo data) using the Gradle wrapper command:
   ```bash
   ./gradlew load
   ```
2. Start the embedded Jetty server using:
   ```bash
   ./gradlew run
   ```
3. By default, Moqui connects to the H2 database for runtime operations.

### 3.2 Access the Application

1. Open a browser and navigate to:  
   `http://localhost:8080`
2. Log in using the default admin credentials:
    - Username: `john.doe`
    - Password: `moqui`

These steps were performed successfully and the login was verified.

![Screen](images/img.png)

***

## Step 4: Create the `moqui-training` Component

IntelliJ IDEA was used as the IDE for Moqui development.

### 4.1 Import Project into IntelliJ IDEA

1. Open IntelliJ IDEA.
2. Go to: **File → New → Project from Existing Sources**.
3. Browse and select the `sandbox` project directory.
4. Choose **Import project from external model** and select **Gradle**.
5. Complete the import process to open the project in IntelliJ.

The Moqui project was successfully imported and opened in IntelliJ IDEA.

### 4.2 Create Component Directory Structure

Inside the `runtime/component` directory:

1. A new directory named `moqui-training` was created.
2. Inside `moqui-training`, the standard directories were created:
    - `entity`
    - `data`
    - `screen`
    - `script`
    - `service`
    - `src`

This directory structure follows the recommended Moqui component layout.

### 4.3 Create `component.xml`

Inside the `moqui-training` directory, a `component.xml` file was created with the following content:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<component xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="http://moqui.org/xsd/moqui-conf-3.xsd"
           name="moqui-training" version="1.0.0"/>
```

This defines the `moqui-training` component with its name and version, and references the Moqui configuration schema.

***

## IntelliJ XML Schema Warning and Fix

IntelliJ IDEA showed a schema warning on `component.xml` for the URL `http://moqui.org/xsd/moqui-conf-3.xsd`.

To resolve this:

1. Open **Settings** → **Languages & Frameworks** → **Schemas and DTDs** → **XML Schemas**.
2. Click the **+** icon to add a new XML Schema mapping.
3. Paste the schema URL `http://moqui.org/xsd/moqui-conf-3.xsd` as the schema location.
4. Map it to the corresponding XSD file in the project.
5. Click **Apply** and **OK**.

After this configuration, the schema warning in `component.xml` was resolved.

***

## Deliverables

By completing the above steps, the following deliverables have been achieved:

- Moqui Framework installed and running on the local Windows machine.
- IntelliJ IDEA successfully configured for Moqui development and the `moqui-training` component created inside the Moqui runtime.


***

# Module 0: MySQL Database Setup

This README describes the steps followed to configure **MySQL** for use with the **Moqui Framework** and verify that Moqui is successfully connected to the MySQL database.

***

## 1. Install MySQL

1.1 MySQL was already installed on the system.  
The version was verified using the command:

```bash
mysql -V
```

The output was:

```text
mysql  Ver 8.0.42 for Win64 on x86_64 (MySQL Community Server - GPL)
```

This version matches the requirement mentioned in the GitHub guide.

1.2 The MySQL installation was secured using:

```bash
mysql_secure_installation
```

During this process, the following options were selected:

- Set the root password? → **Yes** (a strong password was set).
- Remove anonymous users? → **Yes** (anonymous access to the database was disabled).
- Disallow root login remotely? → **Yes** (to prevent unauthorized remote access to the root account).
- Remove test database and access to it? → **Yes** (the default test database was removed to enhance security).
- Reload privilege tables now? → **Yes** (to apply the changes immediately).

All steps in the secure installation process were completed successfully.

***

## 2. Add JDBC Driver

The MySQL JDBC driver Connector was downloaded and the JAR file was placed into the `runtime/lib` directory of the Moqui setup so that Moqui can connect to the MySQL database using JDBC.

***

## 3. Create Database and User

Using **MySQL Workbench**, the database and user for Moqui were created.

### 3.1 Create the `moqui` Database

The following SQL command was executed to create the database with UTF-8 character set:

```sql
CREATE DATABASE moqui CHARACTER SET utf8;
```

### 3.2 Create the `moqui` User

A dedicated user was created with full access to the `moqui` database using:

```sql
CREATE USER 'moqui'@'localhost' IDENTIFIED BY '@12345abC';
GRANT ALL PRIVILEGES ON moqui.* TO 'moqui'@'localhost';
FLUSH PRIVILEGES;
```

### 3.3 Verification

To verify that the database and user were created correctly:

1. The list of databases was checked using:

   ```sql
   SHOW DATABASES;
   ```

   The `moqui` database was present in the list.

2. The grants for the `moqui` user were checked using:

   ```sql
   SHOW GRANTS FOR 'moqui'@'localhost';
   ```

   The output included:

   ```sql
   GRANT USAGE ON *.* TO 'moqui'@'localhost';
   GRANT ALL PRIVILEGES ON `moqui`.* TO 'moqui'@'localhost';
   ```

This confirms that the `moqui` user exists and has full access to the `moqui` database.
![Output](images/img_1.png)
![Output](images/img_2.png)
***

## 4. Update Moqui Configuration

The Moqui configuration was updated to point to the MySQL database.

1. In IntelliJ IDEA, the file `runtime/conf/MoquiDevConf.xml` was opened.
2. The following default properties were added:

```xml
<!-- Mysql Setup -->
<default-property name="entity_ds_db_conf" value="mysql8"/>
<default-property name="entity_ds_host" value="127.0.0.1"/>
<default-property name="entity_ds_user" value="root"/>
<default-property name="entity_ds_password" value="********"/>
<default-property name="entity_ds_database" value="moqui"/>
```

These properties configure Moqui to use the MySQL 8 database configuration, connect to the local host (`127.0.0.1`), and use the specified user, password, and database name.

***

## 5. Load Data and Verify Integration

### 5.1 Load Data

1. Navigated to the `moqui-framework` directory in the terminal.
2. Ran the following command to load data into the configured MySQL database:

```bash
./gradlew load
```

### 5.2 Verification in Moqui UI

After loading the data, the Moqui System Dashboard was used to verify that MySQL integration was successful.

1. Opened the browser and navigated to:

   ```text
   http://localhost:8080/vapps/system/dashboard
   ```

2. In the **Entity Group** section:
    - Confirmed that the transactional entity group is using the `moqui` database on `127.0.0.1` with MySQL.

3. In the **Components** section:
    - Confirmed that the `moqui-training`, `tools`, and `webroot` components are listed with their versions and paths.

Seeing the correct database (`moqui`) and the expected components on the System Dashboard confirms that Moqui connected to MySQL successfully and that the data was loaded correctly.

***

## Status

All required steps for the **MySQL database setup for Moqui** have been completed successfully:

- MySQL installed and secured.
- JDBC driver added to the Moqui runtime.
- `moqui` database and `moqui` user created and verified.
- `MoquiDevConf.xml` updated with MySQL configuration.
- Data loaded and connection verified through the Moqui System Dashboard.