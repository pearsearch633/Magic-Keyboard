import socket
import subprocess
import sys

def simulate_keypress(key):
    # Gebruik osascript om toetsaanslagen te simuleren
    cmd = f'''
    osascript -e 'tell application "System Events" to keystroke "{key}"'
    '''
    subprocess.run(cmd, shell=True)

def start_receiver():
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.bind(('', 5000))
    sock.listen(1)
    
    print("Wachten op verbinding...")
    conn, addr = sock.accept()
    print(f"Verbonden met {addr}")
    
    while True:
        try:
            data = conn.recv(1024).decode()
            if not data:
                break
                
            if data.startswith('KEY:'):
                key = data.split(':')[1].strip()
                simulate_keypress(key)
                
        except Exception as e:
            print(f"Fout: {e}")
            break
    
    conn.close()
    sock.close()

if __name__ == "__main__":
    start_receiver()
