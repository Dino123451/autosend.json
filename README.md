import os
import sys
import time
import requests
import json
import re
from datetime import datetime
from pystyle import Add, Center, Anime, Colors, Colorate, Write, System
from time import sleep
import base64 
 
# Mã màu ANSI
trang = "\033[1;37m"
xanh_la = "\033[1;32m"
xanh_duong = "\033[1;34m"
do = "\033[1;31m"
vang = "\033[1;33m"
tim = "\033[1;35m"
xanhnhat = "\033[1;36m"
reset_color = "\033[0m"

# Đánh dấu bản quyền
HĐ_tool = trang + " " + trang + "[" + vang + ">Kai<" + trang + "] " + tim + "☞▷"
mquang = trang + " " + trang + "[" + do + "÷_+" + trang + "] " + tim + "☞▷"
thanh = trang + "-------------------------------------------------------------------------"

# Màu cầu vồng
rainbow_colors = [
    "\033[91m",  # Đỏ
    "\033[93m",  # Vàng
    "\033[92m",  # Xanh lá
    "\033[96m",  # Xanh dương nhạt
    "\033[94m",  # Xanh dương
    "\033[95m",  # Tím
    "\033[97m"   # Trắng
]

# Hàm in văn bản với hiệu ứng
def xoss(z):
    for e in z + '\n':
        sys.stdout.write(e)
        sys.stdout.flush()
        time.sleep(0.05)

# in khung màu xanh lồn
def in_khung_thong_bao(text):
    xanh_nhat = "\033[96m"
    reset = "\033[0m"
    padding = 2
    chieu_rong = len(text) + padding * 2

    khung_tren = xanh_nhat + "╔" + "═" * chieu_rong + "╗" + reset
    khung_giua = xanh_nhat + "║" + " " * padding + text + " " * padding + "║" + reset
    khung_duoi = xanh_nhat + "╚" + "═" * chieu_rong + "╝" + reset

    print(khung_tren)
    print(khung_giua)
    print(khung_duoi)

# Hàm in văn bản màu cầu vồng
def in_mau(text):
    noi_dung = ""
    for i, char in enumerate(text):
        noi_dung += rainbow_colors[i % len(rainbow_colors)] + char
    noi_dung += reset_color
    print(noi_dung)

# Hàm in văn bản màu xanh biển nhạt
def in_xanhnhat(text):
    print(f"{xanhnhat}{text}{reset_color}")


# Hàm xóa màn hình
def clear_screen():
    os.system('cls' if os.name == 'nt' else 'clear')

# Banner
def banner():
    clear_screen()
    banner_text = """
\033[94m██╗░░██╗░\033[96m█████╗░██╗
\033[92m██║░██╔╝██\033[93m╔══██╗██║
\033[95m█████═╝░██║░░██║██║
██╔═██╗░██║░░██║\033[94m██║
\033[96m██║░╚██╗╚█████╔╝\033[92m██║
\033[93m╚═╝░░╚═╝░╚════╝░\033[95m╚═╝
══════════════════════════════════════════════
\033[93mTool Messenger by Kai
\033[92mDev : Đỗ Thành đạt
\033[95mUy tín Làm Nên thương hiệu✅
\033[96mZalo: 0382673722
\033[94mFacebook: https://www.facebook.com/share/15V5f2NHSq/
══════════════════════════════════════════════
"""
    print(Colorate.Vertical(Colors.red_to_purple, banner_text))

# Khởi động tool
clear_screen()
xoss(f'{xanh_la} [✧˚] Đang Chạy Vào Tool Kai - Treo Mess........')
xoss(f'{xanhnhat} [✧˚] Mua Tool Ib Kai Nho')
xoss(f'{xanh_duong} [✧˚] Kiểm tra Độ trễ')
xoss(f'{vang} [✧˚] Thành công, đang tiến hành vào tool')
clear_screen()
banner()

class Messenger:
    def __init__(self,cookie):
        self.cookie = cookie
        self.user_id = self.id_user()
        self.fb_dtsg = None
        self.init_params()

    def id_user(self):
        try:
            c_user = re.search(r"c_user=(\d+)", self.cookie).group(1)
            return c_user
        except:
            raise Exception("Cookie không hợp lệ")

    def init_params(self):
        headers = {
            'Cookie': self.cookie,
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36',
            'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8',
            'Accept-Language': 'en-US,en;q=0.9,vi;q=0.8',
            'Sec-Fetch-Dest': 'document',
            'Sec-Fetch-Mode': 'navigate', 
            'Sec-Fetch-Site': 'none',
            'Sec-Fetch-User': '?1',
            'Upgrade-Insecure-Requests': '1'
        }

        try:
            response = requests.get('https://www.facebook.com', headers=headers)
            fb_dtsg_match = re.search(r'"token":"(.*?)"', response.text)
            
            if not fb_dtsg_match:
                response = requests.get('https://mbasic.facebook.com', headers=headers)
                fb_dtsg_match = re.search(r'name="fb_dtsg" value="(.*?)"', response.text)
                
                if not fb_dtsg_match:
                    response = requests.get('https://m.facebook.com', headers=headers)
                    fb_dtsg_match = re.search(r'name="fb_dtsg" value="(.*?)"', response.text)

            if fb_dtsg_match:
                self.fb_dtsg = fb_dtsg_match.group(1)
            else:
                raise Exception("Không thể lấy được fb_dtsg")

        except Exception as e:
            raise Exception(f"Lỗi khi khởi tạo tham số: {str(e)}")

    def gui_tn(self, recipient_id, message):
        timestamp = int(time.time() * 1000)
        offline_threading_id = str(timestamp)
        message_id = str(timestamp)

        data = {
            'thread_fbid': recipient_id,
            'action_type': 'ma-type:user-generated-message',
            'body': message,
            'client': 'mercury',
            'author': f'fbid:{self.user_id}',
            'timestamp': timestamp,
            'source': 'source:chat:web',
            'offline_threading_id': offline_threading_id,
            'message_id': message_id,
            'ephemeral_ttl_mode': '',
            '__user': self.user_id,
            '__a': '1',
            '__req': '1b', 
            '__rev': '1015919737',
            'fb_dtsg': self.fb_dtsg
        }

        headers = {
            'Cookie': self.cookie,
            'User-Agent': 'python-http/0.27.0',
            'Accept': '*/*',
            'Accept-Encoding': 'gzip, deflate, br',
            'Connection': 'keep-alive',
            'Content-Type': 'application/x-www-form-urlencoded',
            'Origin': 'https://www.facebook.com',
            'Host': 'www.facebook.com',
            'Referer': f'https://www.facebook.com/messages/t/{recipient_id}'
        }

        try:
            response = requests.post(
                'https://www.facebook.com/messaging/send/',
                data=data,
                headers=headers
            )
            if response.status_code != 200:
                return {
                    'success': False,
                    'error': 'HTTP_ERROR',
                    'error_description': f'Status code: {response.status_code}'
                }

            if 'for (;;);' in response.text:
                clean_text = response.text.replace('for (;;);', '')
                try:
                    result = json.loads(clean_text)
                    
                    if 'error' in result:
                        return {
                            'success': False,
                            'error': result.get('error'),
                            'error_description': result.get('errorDescription', 'Unknown error')
                        }
                        
                    return {
                        'success': True,
                        'message_id': message_id,
                        'timestamp': timestamp
                    }
                except json.JSONDecodeError:
                    pass 
            
            return {
                'success': True,
                'message_id': message_id,
                'timestamp': timestamp
            }
                
        except Exception as e:
            return {
                'success': False,
                'error': 'REQUEST_ERROR',
                'error_description': str(e)
            }

def send_messages_in_loop(messengers, recipient_ids, message_content, delay):
    while True:
        for recipient_id in recipient_ids:
            for messenger in messengers:
                result = messenger.gui_tn(recipient_id, message_content)
                if result['success']:
                    print(f"\033[97m✅Đã gửi vào \033[1;35mbox {recipient_id} - \033[1;34muser : {messenger.user_id}")
                else:
                    in_xanhnhat(f"Không thể gửi vào box {recipient_id} với user_id {messenger.user_id}: {result['error_description']}")
                time.sleep(delay / (len(messengers) * len(recipient_ids)))  # Chia delay cho tổng số cookie và box
        time.sleep(delay)

def input_page():
    clear_screen()
    banner()
    in_xanhnhat("Treo Mess - Tool Treo Messenger - version 2")
    print(Colorate.Diagonal(Colors.green_to_cyan, "══════════════════════════════════════════════"))
    in_mau("▁▂▃▄▅▆▇▉███░░███░░███░░███░░███░░███░░███░░███")
    in_xanhnhat("══════════════════════════════════════════════")
    print("\033[92mεつ\033[1;35m▄█▀█● \033[92m𝘽𝙤̣𝙣 𝘽𝙤̂́ 𝙃𝙤𝙩 𝙢𝙚𝙨𝙨𝙖𝙜𝙚 𝙉𝙜𝙖̀𝙮 𝘼𝙣𝙠 𝙢𝙖̂́𝙩 𝙚𝙢 𝙡𝙖̀ 𝙣𝙜𝙖̀𝙮 𝙘𝙖̉ 𝙩𝙝𝙚̂́ 𝙜𝙞𝙤̛́𝙞 𝙥𝙝𝙖̉𝙞 𝙨𝙤̛̣ 𝙝𝙖̃𝙞 𝘼𝙣𝙠 🎗️ 📌 ")
    print("\033[93m══════════════════════════════════════════════")
    print("\033[93m𝐂𝐨́ 𝐭𝐢𝐞̂̀𝐧 𝐥𝐚̀𝐦 𝐩𝐡𝐢𝐞̂̀𝐧 𝐛𝐨̂́ 𝐊𝐚𝐢💳💍")
    print("\033[1;35m𝑨𝒌𝒂 𝑯𝒐𝒂 𝒎𝒂̃𝒏 𝑳𝒂̂𝒖 𝑭𝒓𝒗 𝑵𝒈𝒖𝒚𝒆𝒏 𝑻𝒉𝒂𝒐 𝑽𝒚 🍼 🍬 🎐")
    print("\033[92m════════════════════════\033[95m══════════════════════")
    print("\033[1;33m𝑇𝑜𝑜𝑙 𝑇𝑖́𝑐ℎ 𝐻𝑜̛̣𝑝 𝐶𝑜𝑑𝑒 𝑉𝑖𝑝, 𝑉𝑎̀ 𝐾ℎ𝑖 𝑀𝑢𝑎 𝐵𝑎̣𝑛 \033[94m𝑆𝑒̃ 𝑐𝑜́ 𝟷 𝑓𝑖𝑙𝑒 ℎ𝑑.𝑝𝑦 𝐾ℎ𝑖 𝑏𝑎̣𝑛 𝑐ℎ𝑎̣𝑦 𝑛𝑜́ 𝑆𝑒̃ 𝐻𝑢̛𝑜̛́𝑛𝑔 𝑑𝑎̂̃𝑛 𝐵𝑎̣𝑛 𝑠𝑢̛̉ 𝑑𝑢̣𝑛𝑔 𝑡𝑜𝑜𝑙🐳")
    print(Colorate.Diagonal(Colors.green_to_cyan, "══════════════════════════════════════════════"))

def main():
    try:
        # Chuyển sang trang nhập thông tin
        input_page()

        # Yêu cầu mật khẩu

        encoded_pass = "a2FpMjAwNQ=="  
        in_khung_thong_bao("Nhập mật khẩu:")
        password_input = input(f"{HĐ_tool}{xanhnhat}").strip()
        if password_input != base64.b64decode(encoded_pass).decode():
            print("\033[1;32mSai mật khẩu! Biến Lẹ =))")
            time.sleep(2)
            return


        # Nhập số lượng ID box
        while True:
            try:
                in_xanhnhat("Nhập số lượng ID box:")
                num_boxes = int(input(f"{HĐ_tool}{xanhnhat}"))
                if num_boxes < 1:
                    raise ValueError("Số lượng ID box phải lớn hơn 0")
                break
            except ValueError:
                in_xanhnhat("Vui lòng nhập một số hợp lệ")

        # Nhập danh sách ID box
        recipient_ids = []
        in_xanhnhat(f"Nhập {num_boxes} ID box (mỗi ID trên một dòng, nhấn Enter sau mỗi ID):")
        for i in range(num_boxes):
            while True:
                in_xanhnhat(f"ID box {i+1}:")
                box_id = input(f"{HĐ_tool}{xanhnhat}").strip()
                if box_id and box_id.isdigit():
                    recipient_ids.append(box_id)
                    break
                in_xanhnhat("ID box phải là số hợp lệ,vui lòng nhập lại")

        if len(recipient_ids) != num_boxes:
            raise Exception(f"Đã nhập {len(recipient_ids)} ID box, nhưng yêu cầu {num_boxes} ID box")

        in_xanhnhat(f"Đã tải {len(recipient_ids)} ID box")

        # Nhập số lượng cookie
        while True:
            try:
                in_xanhnhat("Nhập số lượng cookie:")
                num_cookies = int(input(f"{HĐ_tool}{xanhnhat}"))
                if num_cookies < 1:
                    raise ValueError("Số lượng cookie phải lớn hơn 0")
                break
            except ValueError:
                in_xanhnhat("Vui lòng nhập một số hợp lệ")

        # Nhập danh sách cookie
        cookies = []
        in_xanhnhat(f"Nhập {num_cookies} cookie (mỗi cookie trên một dòng, nhấn Enter sau mỗi cookie):")
        for i in range(num_cookies):
            while True:
                in_xanhnhat(f"Cookie {i+1}:")
                cookie = input(f"{HĐ_tool}{xanhnhat}").strip()
                if cookie:
                    cookies.append(cookie)
                    break
                in_xanhnhat("Cookie không được để trống, vui lòng nhập lại")

        # Kiểm tra số lượng cookie nhập vào
        if len(cookies) != num_cookies:
            in_xanh(f"Đã nhập {len(cookies)} cookie, nhưng yêu cầu {num_cookies} cookie")
            time.sleep(2)
            return

        in_xanhnhat(f"Đã tải {len(cookies)} cookie")

        # Khởi tạo danh sách Messenger
        messengers = []
        for i, cookie in enumerate(cookies, 1):
            try:
                messenger = Messenger(cookie)
                messengers.append(messenger)
                in_xanhnhat(f"Cookie {i}: Đã lấy được id user : {messenger.user_id}")
            except Exception as e:
                in_xanhnhat(f"Cookie {i}: Không hợp lệ - {str(e)}")
        
        if not messengers:
            raise Exception("Không có cookie nào hợp lệ")

        # Nhập tên file
        in_xanhnhat("Nhập tên file txt:")
        file_name = input(f"{HĐ_tool}{xanhnhat}").strip()
        try:
            with open(file_name, 'r', encoding='utf-8') as f:
                message_content = f.read().strip()
                
            if not message_content:
                raise Exception("File txt không có nội dung")

        except FileNotFoundError:
            raise Exception(f"Không tìm thấy file: {file_name}")

        # Nhập delay
        while True:
            try:
                in_xanhnhat("Nhập delay (giây):")
                delay = float(input(f"{HĐ_tool}{xanhnhat}"))
                if delay <= 0:
                    raise ValueError("Delay phải lớn hơn 0")
                break
            except ValueError:
                in_xanhnhat("Vui lòng nhập một số hợp lệ")

        # Chuyển sang trang chạy tool
        clear_screen()
        banner()
        print(f"\033[1;33mĐỗ Thành đạt \033[91m⚀ ⚁ ⚂ ⚃ ⚄ ⚅ \033[1;34m▄︻╦芫≡══-- \033[92m𐐘𓍿𓂸ඞ")
        print(f"\033[95m═══════════════════════\033[96m═══════════════════════")
        print(f"\033[1;32mBắt đầu gửi tin nhắn vào {len(recipient_ids)} box với {len(messengers)} cookie, delay {delay}s")
        print(Colorate.Diagonal(Colors.green_to_cyan, "══════════════════════════════════════════════"))

        # Chạy vòng lặp gửi tin nhắn
        send_messages_in_loop(messengers, recipient_ids, message_content, delay)

    except Exception as e:
        in_xanhnhat(f"Lỗi: {str(e)}")
        time.sleep(3)
    except KeyboardInterrupt:
        in_xanhnhat("Đã Dừng Treo Messenger")
        time.sleep(2)

if __name__ == "__main__":
    main()
