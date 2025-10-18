import datetime
import math
import random
import time
import os
import sys
import yt_dlp





#下載音樂（新增錯誤處理與重試）
def download_music(url, filename, retries=3, delay=5):
    ydl_opts = {
        'format': 'bestaudio/best',
        'outtmpl': filename,
        'postprocessors': [{
            'key': 'FFmpegExtractAudio',
            'preferredcodec': 'mp3',
            'preferredquality': '192',
        }],
    }
    for attempt in range(1, retries + 1):
        try:
            with yt_dlp.YoutubeDL(ydl_opts) as ydl:
                ydl.download([url])
            return filename
        except Exception as e:
            # 只記錄錯誤並嘗試重試，最後放棄但不讓程式崩潰
            print(f"下載失敗（嘗試 {attempt}/{retries}）：{e}")
            if attempt < retries:
                time.sleep(delay * attempt)
            else:
                print("下載失敗，已放棄。將以本地檔案或不播放音效繼續執行。")
                return None

#下載範例音樂（失敗時不拋出例外）

if not os.path.exists("example.mp3"):
    download_result = download_music(input('enter url!'), input('file name!'))
    if download_result is None:
        # 可選：放一個備援的本地檔案名稱或直接跳過
        print("example.mp3 未取得，程式將繼續但不會載入該音效。")

#下載播放清單
def download_playlist(playlist_url, download_path='downloads', retries=3, delay=5):
    if not os.path.exists(download_path):
        os.makedirs(download_path)
    
    ydl_opts = {
        'format': 'bestaudio/best',
        'outtmpl': os.path.join(download_path, '%(title)s.%(ext)s'),
        'postprocessors': [{
            'key': 'FFmpegExtractAudio',
            'preferredcodec': 'mp3',
            'preferredquality': '192',
        }],
        'ignoreerrors': True,
    }
    
    for attempt in range(1, retries + 1):
        try:
            with yt_dlp.YoutubeDL(ydl_opts) as ydl:
                ydl.download([playlist_url])
            print("播放清單下載完成。")
            return
        except Exception as e:
            print(f"播放清單下載失敗（嘗試 {attempt}/{retries}）：{e}")
            if attempt < retries:
                time.sleep(delay * attempt)
            else:
                print("播放清單下載失敗，已放棄。將以本地檔案或不播放音效繼續執行。")
                return

