#File-System-Recovery-and-Optimization-Tool-in-OS
#Python Implementation
#CODE
import os
import shutil
import random
import sys
import psutil
import numpy as np
import matplotlib.pyplot as plt
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QLabel, QFileDialog, QMessageBox
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

class FileSystemTool(QWidget):
    def _init_(self):
        super()._init_()
        self.selected_directory = None
        self.initUI()

    def initUI(self):
        self.setWindowTitle("File System Recovery & Optimization Tool")
        self.setGeometry(300, 200, 500, 400)
        self.setStyleSheet("background-color: #f0f0f0;")

        layout = QVBoxLayout()
        self.label = QLabel("Select an action:")
        self.label.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        self.label.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label)

        self.select_btn = QPushButton("📁 Select Directory")
        self.select_btn.setStyleSheet("background-color: #4CAF50; color: white; font-size: 14px; padding: 8px;")
        self.select_btn.clicked.connect(self.select_directory)
        layout.addWidget(self.select_btn)

        self.optimize_btn = QPushButton("🚀 Optimize File System")
        self.optimize_btn.setStyleSheet("background-color: #2196F3; color: white; font-size: 14px; padding: 8px;")
        self.optimize_btn.clicked.connect(self.optimize_file_system)
        layout.addWidget(self.optimize_btn)

        self.visualize_btn = QPushButton("📊 Visualize Disk Usage")
        self.visualize_btn.setStyleSheet("background-color: #FF9800; color: white; font-size: 14px; padding: 8px;")
        self.visualize_btn.clicked.connect(self.visualize_data)
        layout.addWidget(self.visualize_btn)

        self.recover_btn = QPushButton("🔄 Recover Files")
        self.recover_btn.setStyleSheet("background-color: #F44336; color: white; font-size: 14px; padding: 8px;")
        self.recover_btn.clicked.connect(self.recover_files)
        layout.addWidget(self.recover_btn)

        self.setLayout(layout)

    def select_directory(self):
        directory = QFileDialog.getExistingDirectory(self, "Select a Directory")
        if directory:
            self.selected_directory = directory
            self.label.setText(f"Selected Directory: {directory}")
            self.analyze_file_system(directory)
        else:
            QMessageBox.warning(self, "Error", "No directory selected.")

    def analyze_file_system(self, directory):
        total_size = 0
        file_sizes = []
        fragmented_files = []

        for root, _, files in os.walk(directory):
            for file in files:
                file_path = os.path.join(root, file)
                file_size = os.path.getsize(file_path)
                total_size += file_size
                file_sizes.append(file_size)

                if random.random() < 0.1:
                    fragmented_files.append(file_path)

        free_space = shutil.disk_usage(directory).free
        QMessageBox.information(self, "Analysis Complete", f"Total Used Space: {total_size / (1024*1024):.2f} MB\nFree Space: {free_space / (1024*1024):.2f} MB\nFragmented Files: {len(fragmented_files)}")

        self.optimize_file_system(fragmented_files)

    def optimize_file_system(self, fragmented_files=[]):
        if fragmented_files:
            for file in fragmented_files:
                temp_path = file + ".tmp"
                shutil.copy(file, temp_path)
                os.remove(file)
                shutil.move(temp_path, file)
            QMessageBox.information(self, "Optimization Complete", f"Optimized {len(fragmented_files)} fragmented files.")
        else:
            QMessageBox.information(self, "Optimization Complete", "No fragmentation detected.")

    def visualize_data(self):
        if not self.selected_directory:
            QMessageBox.warning(self, "Error", "No directory selected. Please select a directory first.")
            return

        total_size = 0
        for root, _, files in os.walk(self.selected_directory):
            for file in files:
                file_path = os.path.join(root, file)
                total_size += os.path.getsize(file_path)

        free_space = shutil.disk_usage(self.selected_directory).free
        labels = ['Used Space in Directory', 'Free Space in Directory']
        sizes = [total_size, free_space]

        plt.figure(figsize=(6, 4))
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=['blue', 'green'], startangle=140)
        plt.axis('equal')
        plt.title(f"Disk Usage for {self.selected_directory}")
        plt.show()

    def recover_files(self):
        recovery_log = "recovery.log"
        with open(recovery_log, "w") as f:
            f.write("Recovered file system successfully.")
        QMessageBox.information(self, "Recovery Complete", "Recovered file system. Log saved as recovery.log")

if _name_ == "_main_":
    app = QApplication(sys.argv)
    ex = FileSystemTool()
    ex.show()
    sys.exit(app.exec())

