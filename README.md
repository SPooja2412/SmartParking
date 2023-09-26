# SmartParking
my project
import RPi.GPIO as GPIO
import time
from flask import Flask, render_template

app = Flask(__name__)

# GPIO pin numbers
TRIG_PIN = 18  # Ultrasonic sensor trigger pin
ECHO_PIN = 24  # Ultrasonic sensor echo pin
LED_PIN = 17   # LED pin

# Set up GPIO
GPIO.setmode(GPIO.BCM)
GPIO.setup(TRIG_PIN, GPIO.OUT)
GPIO.setup(ECHO_PIN, GPIO.IN)
GPIO.setup(LED_PIN, GPIO.OUT)

# Function to measure distance from the ultrasonic sensor
def measure_distance():
    GPIO.output(TRIG_PIN, True)
    time.sleep(0.00001)
    GPIO.output(TRIG_PIN, False)

    while GPIO.input(ECHO_PIN) == 0:
        pulse_start = time.time()

    while GPIO.input(ECHO_PIN) == 1:
        pulse_end = time.time()

    pulse_duration = pulse_end - pulse_start
    distance = pulse_duration * 17150
    return round(distance, 2)

# Route for the web interface
@app.route("/")
def index():
    distance = measure_distance()
    if distance < 10:  # Adjust this distance threshold as needed
        GPIO.output(LED_PIN, GPIO.HIGH)
        status = "Occupied"
    else:
        GPIO.output(LED_PIN, GPIO.LOW)
        status = "Vacant"
    return render_template("index.html", status=status)

if __name__ == "__main__":
    try:
        app.run(host="0.0.0.0", port=80)
    except KeyboardInterrupt:
        GPIO.cleanup()
