#!/usr/bin/env python3
from flask import Flask, request, jsonify
import paramiko
import logging
import os

# Cấu hình
SSH_USER = "ubuntu"
CLIENT_IP = "192.168.116.135"  # IP Client
SSH_KEY_PATH = os.path.expanduser("~/.ssh/id_rsa_monitor")  # private key path

app = Flask(__name__)
logging.basicConfig(level=logging.INFO)

def ssh_exec(cmd):
    k = paramiko.RSAKey.from_private_key_file(SSH_KEY_PATH)
    client = paramiko.SSHClient()
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    client.connect(CLIENT_IP, username=SSH_USER, pkey=k)
    stdin, stdout, stderr = client.exec_command(cmd)
    out = stdout.read().decode()
    err = stderr.read().decode()
    client.close()
    return out, err

@app.route('/alert', methods=['POST'])
def alert():
    data = request.json
    app.logger.info("Received alert: %s", data)

    alerts = data.get('alerts', [])
    for a in alerts:
        name = a.get('labels', {}).get('alertname')

        # Handle WebDown
        if name == "WebDown":
            app.logger.info("Handling WebDown - attempting remediation")
            cmd = "docker ps -a --format '{{.Names}}' | grep -w web && docker restart web || docker run -d --name web -p 8081:80 nginx:alpine"
            out, err = ssh_exec(cmd)
            app.logger.info("SSH out: %s", out)
            app.logger.info("SSH err: %s", err)
            return jsonify({"status": "remediation attempted", "out": out, "err": err}), 200

        # Handle HighCPU
        if name == "HighCPU":
            app.logger.info("HighCPU detected - no remediation action taken")
            return jsonify({"status": "HighCPU logged"}), 200

    return jsonify({"status": "no action"}), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
