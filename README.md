import os
from googleapiclient.discovery import build
import matplotlib.pyplot as plt

# YouTube Data API v3 키 설정
api_key = 'AIzaSyA6QG-Pfip47Cdj-y5yMFgq901vw8_bKHk'
youtube = build('youtube', 'v3', developerKey=api_key)

# 검색 쿼리 설정
query = '데이터 시각화'

# YouTube에서 동영상 검색
request = youtube.search().list(
    q=query,
    part='id',
    type='video',
    maxResults=10  # 필요에 따라 조절
)

response = request.execute()

# 결과에서 동영상의 videoId 추출
video_ids = [item['id']['videoId'] for item in response['items']]

# 각 동영상에 대해 동영상의 카테고리 title을 얻기
categories = []
view_counts = []  # 시청 횟수를 저장할 리스트 추가
for video_id in video_ids:
    video_request = youtube.videos().list(
        part='snippet,statistics',
        id=video_id
    )
    video_response = video_request.execute()
    category_title = video_response['items'][0]['snippet']['title']
    categories.append(category_title)
    view_count = int(video_response['items'][0]['statistics']['viewCount'])
    view_counts.append(view_count)

# 장르(카테고리)와 연령대에 따른 데이터 시각화
plt.figure(figsize=(10, 6))
plt.bar(categories, view_counts, color='skyblue')
plt.xlabel('장르(카테고리)')
plt.ylabel('시청 횟수')
plt.title('유튜브 동영상 장르별 시청 통계')
plt.xticks(rotation=45, ha='right')  # X축 라벨을 45도 기울임
plt.show()
