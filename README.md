# Blockchain-Socket enabled Transport for unmanned aircarft

| Column Name                 | Description                                         |
| --------------------------- | --------------------------------------------------- |
| `Delivery_ID`               | Unique ID for each delivery                         |
| `Drone_ID`                  | Unique identifier of the unmanned aircraft          |
| `Medicine_Name`             | Name of the medicine being delivered                |
| `Dosage_mg`                 | Dosage in mg                                        |
| `Patient_ID`                | Unique identifier for the patient                   |
| `Patient_Location_Lat`      | GPS latitude                                        |
| `Patient_Location_Lon`      | GPS longitude                                       |
| `Delivery_Timestamp`        | Date and time of delivery                           |
| `Edge_Node_ID`              | ID of the edge node processing the data             |
| `Temperature_C`             | Cargo hold temperature during delivery              |
| `Blockchain_Tx_Hash`        | Blockchain transaction hash confirming the delivery |
| `Delivery_Status`           | Status (Delivered / Failed / In Transit)            |
| `Flight_Duration_Min`       | Duration of the drone flight                        |
| `Battery_Percent_Remaining` | Battery level on arrival                            |
| `Verification_Success`      | Blockchain delivery verification status (Yes/No)    |

![image](https://github.com/user-attachments/assets/5ff39f85-6559-4a3a-8e53-73bc8a764280)
import pandas as pd
import numpy as np
from faker import Faker
import random
from datetime import datetime, timedelta

# Initialize Faker and random seed
fake = Faker()
np.random.seed(42)

# Predefined lists
medicine_list = [
    'Insulin', 'Paracetamol', 'Amoxicillin', 'Metformin',
    'Atorvastatin', 'Aspirin', 'Ibuprofen', 'Lisinopril'
]
status_list = ['Delivered', 'Failed', 'In Transit']
edge_node_ids = [f'EDGE-{i:02d}' for i in range(1, 11)]
drone_ids = [f'DRN-{i:04d}' for i in range(1000, 2000)]

# Function to generate a fake blockchain hash
def generate_tx_hash():
    return '0x' + ''.join(random.choices('abcdef0123456789', k=64))

# Generate dataset
def generate_dataset(num_records=10000):
    data = []
    for i in range(num_records):
        delivery_id = f'DEL-{i+1:06d}'
        drone_id = random.choice(drone_ids)
        medicine_name = random.choice(medicine_list)
        dosage = random.choice([50, 100, 250, 500])
        patient_id = f'PAT-{random.randint(1000, 9999)}'
        lat = round(random.uniform(24.0, 26.0), 5)
        lon = round(random.uniform(66.0, 68.0), 5)
        timestamp = fake.date_time_between(start_date='-30d', end_date='now')
        edge_node = random.choice(edge_node_ids)
        temperature = round(random.uniform(2.0, 8.0), 2)
        tx_hash = generate_tx_hash()
        status = random.choices(status_list, weights=[0.85, 0.05, 0.10])[0]
        duration = random.randint(10, 45)
        battery = random.randint(20, 100)
        verified = 'Yes' if status == 'Delivered' else 'No'

        data.append([
            delivery_id, drone_id, medicine_name, dosage, patient_id,
            lat, lon, timestamp, edge_node, temperature, tx_hash,
            status, duration, battery, verified
        ])

    columns = [
        'Delivery_ID', 'Drone_ID', 'Medicine_Name', 'Dosage_mg', 'Patient_ID',
        'Patient_Location_Lat', 'Patient_Location_Lon', 'Delivery_Timestamp',
        'Edge_Node_ID', 'Temperature_C', 'Blockchain_Tx_Hash', 'Delivery_Status',
        'Flight_Duration_Min', 'Battery_Percent_Remaining', 'Verification_Success'
    ]

    df = pd.DataFrame(data, columns=columns)
    return df

# Generate and save CSV
df = generate_dataset()
csv_file = "IoT_UAS_Clinical_Medicine_Delivery.csv"
df.to_csv(csv_file, index=False)
print(f"Dataset saved to: {csv_file}")
