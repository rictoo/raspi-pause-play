import spotipy
import spotipy.util as util
import dbus
from spotipy.oauth2 import SpotifyOAuth
from http.server import BaseHTTPRequestHandler, HTTPServer
import urllib.parse
import subprocess
#import threading
import os

username = '<username>'
client_id = '<spotify client id>'
client_secret = '<client secret>'
redirect_uri='http://localhost:8080'

#dbus_name = [i for i in dbus.SystemBus().list_names() if i.startswith('org.mpris.MediaPlayer2.spotifyd')][0]

def on_name_owner_changed(changed):
	print(changed)

# Connect to the system bus
bus = dbus.SystemBus()

#spotifyd = bus.get_object(dbus_name, "/org/mpris/MediaPlayer2")

#proxy = bus.get_object(dbus_name, '/org/mpris/MediaPlayer2')
#interface = dbus.Interface(proxy, dbus_interface='org.mpris.MediaPlayer2.Player')

scope = 'user-read-playback-state user-modify-playback-state'

spotify = spotipy.Spotify(auth_manager=SpotifyOAuth(
    client_id=client_id, client_secret=client_secret, redirect_uri=redirect_uri, scope=scope, open_browser=False, ))

class RequestHandler(BaseHTTPRequestHandler):
	def do_GET(self):
		self.send_response(200)
		self.end_headers()
		if self.path.startswith("/notify?"):
			query_string = urllib.parse.urlsplit(self.path).query
			params = urllib.parse.parse_qs(query_string)
			url = params.get("url", [""])[0]
			print("Listening to Youtube URL:", url)
			#subprocess.run(["/bin/bash", "/root/youtube.sh", url])
			os.system(f'/root/youtube.sh "{url}" &')
		if self.path == '/hello':
			dbus_name = [i for i in dbus.SystemBus().list_names() if i.startswith('org.mpris.MediaPlayer2.spotifyd')][0]
			bus = dbus.SystemBus()
			proxy = bus.get_object(dbus_name, '/org/mpris/MediaPlayer2')
			interface = dbus.Interface(proxy, dbus_interface='org.mpris.MediaPlayer2.Player')

			interface.PlayPause()
		else:
			self.send_response(404)
			self.end_headers()
	def log_message(self, format, *args):
		return

def run():
	#print('grabbing dbus name...')
	print('starting server...')
	server_address = ('0.0.0.0', 8000)
	httpd = HTTPServer(server_address, RequestHandler)
	print('running server...')
	httpd.serve_forever()
run()
