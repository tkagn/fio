To produce charts and graphs from `fio` results, you can use the **JSON output format** from `fio` and visualize the data with tools like Python (Matplotlib, Pandas), Excel, or other graphing software. Here's a step-by-step guide:

---

### **1. Generate FIO Results in JSON Format**
Add the `--output-format=json` option to your `fio` command. For example:

```bash
fio --name=iops-test --filename=/dev/sdX --ioengine=libaio --direct=1 \
    --rw=randread --bs=4k --iodepth=32 --numjobs=4 --runtime=60 \
    --time_based --group_reporting --output-format=json --output=fio_results.json
```

This saves the results in a JSON file called `fio_results.json`.

---

### **2. Example Structure of JSON Output**
The JSON output will include details like IOPS, bandwidth, latency, and more. Here's a snippet:
```json
{
  "jobs": [
    {
      "jobname": "iops-test",
      "read": {
        "iops": 400,
        "bw": 1600,
        "lat_ns": {
          "min": 10000,
          "max": 75000,
          "mean": 25000
        }
      },
      "write": {
        "iops": 0,
        "bw": 0,
        "lat_ns": {
          "min": 0,
          "max": 0,
          "mean": 0
        }
      }
    }
  ]
}
```

---

### **3. Visualize the Data with Python**
You can use Python libraries like `matplotlib`, `pandas`, or `seaborn` to create graphs from the JSON file.

#### **Example: IOPS Graph**
```python
import json
import matplotlib.pyplot as plt

# Load FIO results from JSON
with open("fio_results.json") as f:
    data = json.load(f)

# Extract metrics
jobs = data["jobs"]
iops = [job["read"]["iops"] for job in jobs]
bandwidth = [job["read"]["bw"] for job in jobs]
job_names = [job["jobname"] for job in jobs]

# Plot IOPS
plt.bar(job_names, iops, color='blue', alpha=0.7)
plt.xlabel("Jobs")
plt.ylabel("IOPS")
plt.title("IOPS Performance")
plt.show()
```

---

#### **Example: Latency Percentiles Chart**
If latency percentiles are included in the JSON:
```python
# Extract latency percentiles
percentiles = jobs[0]["read"]["clat"]["percentile"]
latencies = list(percentiles.keys())
values = list(percentiles.values())

# Plot latency percentiles
plt.plot(latencies, values, marker='o', color='orange')
plt.xscale('log')  # Use log scale for percentiles
plt.xlabel("Percentiles")
plt.ylabel("Latency (ns)")
plt.title("Latency Percentiles")
plt.grid()
plt.show()
```

---

### **4. Use FIO's Built-in Graph Tool**
FIO provides a built-in script called `fio_generate_plots` to create graphs directly from log files. To use it:

#### Enable Logging in Your Test
Add the following options to your `fio` command:
```bash
fio --name=iops-test --filename=/dev/sdX --ioengine=libaio --direct=1 \
    --rw=randread --bs=4k --iodepth=32 --numjobs=4 --runtime=60 \
    --time_based --write_bw_log=bw_log --write_lat_log=lat_log --write_iops_log=iops_log
```

This generates log files (`bw_log.*`, `lat_log.*`, `iops_log.*`) for bandwidth, latency, and IOPS.

#### Generate Graphs
Run the `fio_generate_plots` script (usually installed with `fio`):
```bash
fio_generate_plots bw_log iops_log lat_log
```
This creates `.png` files for each metric.

---

### **5. Visualize with Excel**
1. Open the JSON or log files and parse the data into rows and columns.
2. Use Excel's graphing tools (e.g., bar charts, line graphs) to visualize IOPS, latency, and bandwidth.

---

### **6. Tools for Advanced Visualization**
- **Grafana**: Combine FIO with monitoring tools like Grafana to produce real-time dashboards.
- **Python Libraries**: Use `pandas` and `seaborn` for more advanced analysis.

Would you like help setting up specific graphs or using a particular tool?