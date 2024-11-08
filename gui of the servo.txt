import tkinter as tk
import serial
import time

# Establish a serial connection (adjust COM port/baud rate as needed)
ser = serial.Serial('COM3', 9600, timeout=1)  # Replace 'COM3' with your Pico's port

# Initialize the main GUI window
root = tk.Tk()
root.title("Fin Test Interface")
root.geometry("300x200")

# Feedback label for showing angle and status
feedback_label = tk.Label(root, text="Press 'Fin Test' to begin.")
feedback_label.pack(pady=10)

# Function to send command to Pico W to rotate fins
def start_rotation():
    feedback_label.config(text="Fin Test started...")
    ser.write(b"rotate\n")
    read_angle()

# Function to read angles from serial and update the GUI
def read_angle():
    if ser.in_waiting > 0:
        angle = ser.readline().decode('utf-8').strip()
        feedback_label.config(text=f"Current Angle: {angle}°")
        if angle == "45":  # Stop updating at the end of rotation
            feedback_label.config(text="Fin Test Successful")
            return
    root.after(100, read_angle)

# Function to handle Fin Test button click
def fin_test():
    start_button.pack(pady=5)
    cancel_button.pack(pady=5)

# Create buttons
fin_test_button = tk.Button(root, text="Fin Test", command=fin_test)
fin_test_button.pack(pady=20)

start_button = tk.Button(root, text="Start", command=start_rotation)
start_button.pack_forget()

cancel_button = tk.Button(root, text="Cancel", command=root.quit)
cancel_button.pack_forget()

# Run the GUI loop
root.mainloop()
