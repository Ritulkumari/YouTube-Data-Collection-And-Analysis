# YouTube-Data-Collection-And-Analysis
Automated YouTube Data Collection and Analysis for Trends and Insights.
## Table of Contents
1. [Introduction](#introduction)
2. [Features](#features)
3. [Requirements](#requirements)
4. [Installation](#installation)
5. [Usage](#usage)
6. [Data Collection](#data-collection)
7. [Data Ingestion](#data-ingestion)
8. [Data Preprocessing](#data-preprocessing)
9. [Trend Analysis](#trend-analysis)
10. [Contributing](#contributing)
11. [License](#license)
12. [Contact](#contact)

## Introduction
This project focuses on collecting data from YouTube and performing trend analysis to understand viewer preferences, popular topics, and emerging trends. It leverages the YouTube Data API to gather video metadata, comments, and channel statistics, followed by data processing and visualization techniques to analyze trends.

## Features
- **Data Collection**: Retrieve video metadata, comments, and channel statistics using the YouTube Data API.
- **Data Processing**: Clean and preprocess the collected data for analysis.
- **Trend Analysis**: Identify popular topics, trending videos, and channel growth patterns.
- **Visualization**: Generate visual reports and dashboards to present the findings.

## Requirements
- Python 3.x
- Google API Client Library
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Jupyter Notebook (optional, for interactive analysis)

## Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/RitulKumari/youtube-data-analysis.git
   cd youtube-data-analysis

## Usage
1)  Obtain YouTube API Key:
- Go to the Google Cloud Console.
- Create a new project and enable the YouTube Data API v3.
- Create API credentials and obtain your API key.

2) Configure API Key:
- Create a file named config.py in the root directory of the project.
- Add the following line to config.py:
 ``API_KEY = 'YOUR_YOUTUBE_API_KEY'``
## Data Collection
#### Run Data Collection:
- Use the provided Jupyter Notebooks and Python to process and analyze the collected data.  
  ```
  **python**
   def get_trending_videos(api_key, max_results=200):
    # build the youtube service
    youtube = build('youtube', 'v3', developerKey=api_key)

    # initialize the list to hold video details
    videos = []

    # fetch the most popular videos
    request = youtube.videos().list(
        part='snippet,contentDetails,statistics',
        chart='mostPopular',
        regionCode='US',  
        maxResults=50
    )

    # paginate through the results if max_results > 50
    while request and len(videos) < max_results:
        response = request.execute()
        for item in response['items']:
            video_details = {
                'video_id': item['id'],
                'title': item['snippet']['title'],
                'description': item['snippet']['description'],
                'published_at': item['snippet']['publishedAt'],
                'channel_id': item['snippet']['channelId'],
                'channel_title': item['snippet']['channelTitle'],
                'category_id': item['snippet']['categoryId'],
                'tags': item['snippet'].get('tags', []),
                'duration': item['contentDetails']['duration'],
                'definition': item['contentDetails']['definition'],
                'caption': item['contentDetails'].get('caption', 'false'),
                'view_count': item['statistics'].get('viewCount', 0),
                'like_count': item['statistics'].get('likeCount', 0),
                'dislike_count': item['statistics'].get('dislikeCount', 0),
                'favorite_count': item['statistics'].get('favoriteCount', 0),
                'comment_count': item['statistics'].get('commentCount', 0)
            }
            videos.append(video_details)

        # get the next page token
        request = youtube.videos().list_next(request, response)

    return videos[:max_results]
    ```
  - Use the provided scripts to collect data.
  ```bash
    def save_to_csv(data, filename):
    df = pd.DataFrame(data)
    df.to_csv(filename, index=False)

    def main():
        trending_videos = get_trending_videos(API_KEY)
        filename = 'trending_videos.csv'
        save_to_csv(trending_videos, filename)
        print(f'Trending videos saved to {filename}')

    if __name__ == '__main__':
        main()
In the code above, we use the YouTube Data API to get information about the top 200 trending videos in the US. The API returns data in multiple pages, so the script goes through each page to gather details about each video. These details include the title, description, publish date, channel info, tags, video duration, video quality, captions, and engagement metrics like views, likes, and comments. The script then collects all this information into a list, converts it into a pandas DataFrame, and saves it to a CSV file called ``trending_videos.csv``. This allows us to study trends and patterns in the video data we collected.

## Data Ingestion
Now, let’s have a look at what the data looks like:
```bash
  import pandas as pd

  trending_videos = pd.read_csv('trending_videos.csv')
  print(trending_videos.head())
The data we get:

       video_id                                              title  \
  0  H58vbez_m4E                       Kendrick Lamar - Not Like Us   
  1  wT6Vf3PCx6Y                   We Bought A Semi Truck - BIGTIME   
  2  ZsOHVtrecgo  ImDontai Reacts To Kendrick Lamar Not Like Us ...   
  3  XOK1F9TLEH8  Natlan Preview Teaser - Need a Hand? | Genshin...   
  4  Rxw5EVk8XdQ  ITS OVER DRAKE... Kendrick Lamar "Not Like Us"...   

                                         description          published_at  \
0  Kendrick Lamar “Not Like Us”\nDirected by Dave...  2024-07-04T23:00:09Z   
1  Today we're seeing what its like to drive a 50...  2024-07-05T15:01:04Z   
2  In Honor Of MajinCarp\n\nIntro Song https://ww...  2024-07-04T23:50:02Z   
3  Mishaps are inevitable when traveling alone.\n...  2024-07-05T12:50:00Z   
4  ITS OVER DRAKE... Not Like Us (Music Video) RE...  2024-07-05T00:09:32Z   

                 channel_id      channel_title  category_id  \
0  UCoYfzC2zMlc9M-Odgaf6OSg  KendrickLamarVEVO           10   
1  UCxpbaAGBv4zrRJx_AjGdVLA            BigTime            2   
2  UClylk2CQZtH7bSOuNJ66k4A      ImStillDontai           22   
3  UCiS882YPwZt1NfaM0gR0D9Q     Genshin Impact           20   
4  UCideLO7JJw2poa1JrEmQKDQ     Scru Face Jean           10   

                                                tags  duration definition  \
0  ['Kendrick', 'Lamar', 'Not', 'Like', 'under', ...   PT5M55S         hd   
1  ['donut', 'donut media', 'DONUT', 'james pumph...  PT22M35S         hd   
2                                                 []   PT21M8S         hd   
3  ['Amber', 'amber vtuber', 'genshi', 'genshi ga...   PT1M16S         hd   
4  ['kendrick lamar not like us', 'kendrick lamar...  PT16M57S         hd   

   caption  view_count  like_count  dislike_count  favorite_count  \
0     True    22510933     2666967              0               0   
1    False     1006992      122966              0               0   
2    False     1204269       69210              0               0   
3    False      898726       63619              0               0   
4    False      628704       41225              0               0   

   comment_count  
0         194306  
1           9539  
2           3074  
3           6955  
4           4308
```


Now, let’s have a look at the missing values and data types:

```bash
# check for missing values
missing_values = trending_videos.isnull().sum()

# display data types
data_types = trending_videos.dtypes

missing_values, data_types
```
```bash
(video_id          0
 title             0
 description       2
 published_at      0
 channel_id        0
 channel_title     0
 category_id       0
 tags              0
 duration          0
 definition        0
 caption           0
 view_count        0
 like_count        0
 dislike_count     0
 favorite_count    0
 comment_count     0
 dtype: int64,
 video_id          object
 title             object
 description       object
 published_at      object
 channel_id        object
 channel_title     object
 category_id        int64
 tags              object
 duration          object
 definition        object
 caption             bool
 view_count         int64
 like_count         int64
 dislike_count      int64
 favorite_count     int64
 comment_count      int64
 dtype: object)
```
## Data Preprocessing
The description column has 2 missing values. This is minor and can be handled as needed. The data types seem appropriate for most columns, but we may need to convert the published_at column to a datetime format and tags might need further processing. Let’s fix these changes:

```bash
# fill missing descriptions with "No description"
trending_videos['description'].fillna('No description', inplace=True)

# convert `published_at` to datetime
trending_videos['published_at'] = pd.to_datetime(trending_videos['published_at'])

# convert tags from string representation of list to actual list
trending_videos['tags'] = trending_videos['tags'].apply(lambda x: eval(x) if isinstance(x, str) else x)
```
Now, before moving forward with the analysis, let’s have a quick look at the descriptive statistics:

![image](https://github.com/Ritulkumari/YouTube-Data-Collection-And-Analysis/assets/98699438/463f8cb5-4c69-456a-9b33-05c0bc9e4ba5)

## Trend Analysis

Now, let’s have a look at the distribution of views, likes and comments of all the videos in the data:
```bash
import matplotlib.pyplot as plt
import seaborn as sns
sns.set(style="whitegrid")

fig, axes = plt.subplots(1, 3, figsize=(18, 5))

# view count distribution
sns.histplot(trending_videos['view_count'], bins=30, kde=True, ax=axes[0], color='blue')
axes[0].set_title('View Count Distribution')
axes[0].set_xlabel('View Count')
axes[0].set_ylabel('Frequency')

# like count distribution
sns.histplot(trending_videos['like_count'], bins=30, kde=True, ax=axes[1], color='green')
axes[1].set_title('Like Count Distribution')
axes[1].set_xlabel('Like Count')
axes[1].set_ylabel('Frequency')

# comment count distribution
sns.histplot(trending_videos['comment_count'], bins=30, kde=True, ax=axes[2], color='red')
axes[2].set_title('Comment Count Distribution')
axes[2].set_xlabel('Comment Count')
axes[2].set_ylabel('Frequency')

plt.tight_layout()
plt.show()
```
![image](https://github.com/Ritulkumari/YouTube-Data-Collection-And-Analysis/assets/98699438/a96210e1-a8f2-4d0e-bf1a-9f36777e75a7)

The histograms show that the distributions of view counts, like counts, and comment counts are right-skewed, with most videos having lower counts and a few videos having very high counts. 

Now, let’s have a look at the correlation between likes, views, and comments:
![image](https://github.com/Ritulkumari/YouTube-Data-Collection-And-Analysis/assets/98699438/88491d81-6d7c-47f9-9df8-60e8747a25f5)

let’s collect the category names as well to analyze the categories of the trending videos:
```bash
def get_category_mapping():
    request = youtube.videoCategories().list(
        part='snippet',
        regionCode='US'
    )
    response = request.execute()
    category_mapping = {}
    for item in response['items']:
        category_id = int(item['id'])
        category_name = item['snippet']['title']
        category_mapping[category_id] = category_name
    return category_mapping

# get the category mapping
category_mapping = get_category_mapping()
print(category_mapping)
```
![image](https://github.com/Ritulkumari/YouTube-Data-Collection-And-Analysis/assets/98699438/765c60ee-5c18-408c-a4ab-2d7838c43e63)

Now, here’s how we can analyze the number of trending videos by category:

```bash
trending_videos['category_name'] = trending_videos['category_id'].map(category_mapping)

# Bar chart for category counts
plt.figure(figsize=(12, 8))
sns.countplot(y=trending_videos['category_name'], order=trending_videos['category_name'].value_counts().index, palette='viridis')
plt.title('Number of Trending Videos by Category')
plt.xlabel('Number of Videos')
plt.ylabel('Category')
plt.show()
```
![image](https://github.com/Ritulkumari/YouTube-Data-Collection-And-Analysis/assets/98699438/d052e250-ce22-4649-a0c6-f80878f71a9f)

The bar chart shows that the Gaming, Entertainment, Sports, and Music categories have the highest number of trending videos.
Now, let’s have a look at the average engagement metrics by category:
```bash
# average engagement metrics by category
category_engagement = trending_videos.groupby('category_name')[['view_count', 'like_count', 'comment_count']].mean().sort_values(by='view_count', ascending=False)

fig, axes = plt.subplots(1, 3, figsize=(18, 10))

# view count by category
sns.barplot(y=category_engagement.index, x=category_engagement['view_count'], ax=axes[0], palette='viridis')
axes[0].set_title('Average View Count by Category')
axes[0].set_xlabel('Average View Count')
axes[0].set_ylabel('Category')

# like count by category
sns.barplot(y=category_engagement.index, x=category_engagement['like_count'], ax=axes[1], palette='viridis')
axes[1].set_title('Average Like Count by Category')
axes[1].set_xlabel('Average Like Count')
axes[1].set_ylabel('')

# comment count by category
sns.barplot(y=category_engagement.index, x=category_engagement['comment_count'], ax=axes[2], palette='viridis')
axes[2].set_title('Average Comment Count by Category')
axes[2].set_xlabel('Average Comment Count')
axes[2].set_ylabel('')

plt.tight_layout()
plt.show()
```
![image](https://github.com/Ritulkumari/YouTube-Data-Collection-And-Analysis/assets/98699438/a64c24db-646b-4552-b403-1e268e5d7aca)

Music and People & Blogs categories have the highest average view counts, likes, and comments. Film & Animation also shows high engagement, especially in view counts and like counts.

Now, let’s analyze the content and duration of the videos. But first, we need to convert the duration from ISO 8601 format to seconds:

```bash
!pip install isodate
import isodate

# convert ISO 8601 duration to seconds
trending_videos['duration_seconds'] = trending_videos['duration'].apply(lambda x: isodate.parse_duration(x).total_seconds())

trending_videos['duration_range'] = pd.cut(trending_videos['duration_seconds'], bins=[0, 300, 600, 1200, 3600, 7200], labels=['0-5 min', '5-10 min', '10-20 min', '20-60 min', '60-120 min'])
```
In the above code, we are using the isodate library to convert the duration of each video from the ISO 8601 format to seconds, which allows for numerical analysis. After converting the durations, we are categorizing the videos into different duration ranges (0-5 minutes, 5-10 minutes, 10-20 minutes, 20-60 minutes, and 60-120 minutes) by creating a new column called duration_range. This categorization enables us to analyze and compare the engagement metrics of videos within specific length intervals, providing insights into how video length influences viewer behaviour and video performance.

Now, let’s analyze the content and the duration of videos:
```bash
# scatter plot for video length vs view count
plt.figure(figsize=(10, 6))
sns.scatterplot(x='duration_seconds', y='view_count', data=trending_videos, alpha=0.6, color='purple')
plt.title('Video Length vs View Count')
plt.xlabel('Video Length (seconds)')
plt.ylabel('View Count')
plt.show()

# bar chart for engagement metrics by duration range
length_engagement = trending_videos.groupby('duration_range')[['view_count', 'like_count', 'comment_count']].mean()

fig, axes = plt.subplots(1, 3, figsize=(18, 8))

# view count by duration range
sns.barplot(y=length_engagement.index, x=length_engagement['view_count'], ax=axes[0], palette='magma')
axes[0].set_title('Average View Count by Duration Range')
axes[0].set_xlabel('Average View Count')
axes[0].set_ylabel('Duration Range')

# like count by duration range
sns.barplot(y=length_engagement.index, x=length_engagement['like_count'], ax=axes[1], palette='magma')
axes[1].set_title('Average Like Count by Duration Range')
axes[1].set_xlabel('Average Like Count')
axes[1].set_ylabel('')

# comment count by duration range
sns.barplot(y=length_engagement.index, x=length_engagement['comment_count'], ax=axes[2], palette='magma')
axes[2].set_title('Average Comment Count by Duration Range')
axes[2].set_xlabel('Average Comment Count')
axes[2].set_ylabel('')

plt.tight_layout()
plt.show()
```
![image](https://github.com/Ritulkumari/YouTube-Data-Collection-And-Analysis/assets/98699438/1250303b-5afe-4324-a9a9-b3733cc4476c)

![image](https://github.com/Ritulkumari/YouTube-Data-Collection-And-Analysis/assets/98699438/5c195bfa-265b-451d-bae8-3099935db534)

The scatter plot shows a slight negative correlation between video length and view count, indicating shorter videos tend to have higher view counts. Videos in the 0-5 minute range have the highest average view counts, likes, and comments. Engagement decreases as video length increases.

Now, let’s analyze the relationship between views and number of tags used in the video:

```bash
# calculate the number of tags for each video
trending_videos['tag_count'] = trending_videos['tags'].apply(len)

# scatter plot for number of tags vs view count
plt.figure(figsize=(10, 6))
sns.scatterplot(x='tag_count', y='view_count', data=trending_videos, alpha=0.6, color='orange')
plt.title('Number of Tags vs View Count')
plt.xlabel('Number of Tags')
plt.ylabel('View Count')
plt.show()
```

![image](https://github.com/Ritulkumari/YouTube-Data-Collection-And-Analysis/assets/98699438/ada2e13b-dccd-41d3-aceb-6af2b8c50349)

The scatter plot shows a very weak relationship between the number of tags and view count, suggesting that the number of tags has minimal impact on a video’s view count.

Now, let’s see if there’s an impact of the time a video is posted on its views:

```bash
# extract hour of publication
trending_videos['publish_hour'] = trending_videos['published_at'].dt.hour

# bar chart for publish hour distribution
plt.figure(figsize=(12, 6))
sns.countplot(x='publish_hour', data=trending_videos, palette='coolwarm')
plt.title('Distribution of Videos by Publish Hour')
plt.xlabel('Publish Hour')
plt.ylabel('Number of Videos')
plt.show()

# scatter plot for publish hour vs view count
plt.figure(figsize=(10, 6))
sns.scatterplot(x='publish_hour', y='view_count', data=trending_videos, alpha=0.6, color='teal')
plt.title('Publish Hour vs View Count')
plt.xlabel('Publish Hour')
plt.ylabel('View Count')
plt.show()
```
![Screenshot 2024-07-07 092557](https://github.com/Ritulkumari/YouTube-Data-Collection-And-Analysis/assets/98699438/0031c827-557b-401b-9b44-60abc392cc91)

![image](https://github.com/Ritulkumari/YouTube-Data-Collection-And-Analysis/assets/98699438/0372c3d8-9e04-4733-8cce-60b4cad60def)

The distribution shows that most videos are published between 14:00 and 20:00 hours (2 PM – 8 PM), indicating this may be an optimal time for uploading videos. There is a very weak negative relationship between publish hour and view count, suggesting that the hour of publication has minimal impact on engagement metrics.

### Conclusion

So, here’s my conclusion on what makes a video trend on YouTube:

- Encourage viewers to like and comment on videos to boost engagement metrics.
- Aim to create shorter videos (under 5 minutes) for higher engagement, especially for categories like Music and Entertainment.
- Schedule video uploads around peak times (2 PM – 8 PM) to maximize initial views and engagement.














  
