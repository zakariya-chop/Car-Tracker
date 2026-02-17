from flask import Flask, render_template_string
import folium
import requests  # لجلب بيانات GPS إذا لزم الأمر

app = Flask(__name__)

# بيانات سيارة افتراضية (يمكن استبدالها ببيانات حقيقية من GPS)
car_location = {"lat": 37.7749, "lon": -122.4194, "speed": 60}  # سان فرانسيسكو كمثال

@app.route('/')
def home():
    # إنشاء خريطة باستخدام Folium
    m = folium.Map(location=[car_location["lat"], car_location["lon"]], zoom_start=12)
    folium.Marker([car_location["lat"], car_location["lon"]], 
                  popup=f"Speed: {car_location['speed']} km/h").add_to(m)
    
    # تحويل الخريطة إلى HTML
    map_html = m._repr_html_()
    
    # قالب HTML بسيط
    html_template = f"""
    <!DOCTYPE html>
    <html>
    <head><title>Car Tracker</title></head>
    <body>
    <h1>Car Tracking Application</h1>
    <p>Current Location: Lat {car_location["lat"]}, Lon {car_location["lon"]}</p>
    {map_html}
    </body>
    </html>
    """
    return render_template_string(html_template)

if __name__ == '__main__':
    app.run(debug=True)

    import sqlite3

def save_location(lat, lon, speed):
    conn = sqlite3.connect('car_tracker.db')
    c = conn.cursor()
    c.execute('CREATE TABLE IF NOT EXISTS locations (id INTEGER PRIMARY KEY, lat REAL, lon REAL, speed REAL)')
    c.execute('INSERT INTO locations (lat, lon, speed) VALUES (?, ?, ?)', (lat, lon, speed))
    conn.commit()
    conn.close()
