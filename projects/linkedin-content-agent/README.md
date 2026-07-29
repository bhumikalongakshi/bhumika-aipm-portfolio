# Why I built this
Everyone posts something or the other on social media, which takes time to think about the topic, then do research, then create the post. I thought why not automate it and reduce some time. This agent creates a linkedin content and an image as per user prompt. It then sends it to your gmail for review. 
So, all you need to do is tell him a topic and your content will reach in your inbox for review and you simply need to post it. 
Natural question is why not automate the posting as well. Some things need review from human eye, so that if you're not happy with the post you can ask for the changes and will get a refined post.


# What it does

- Takes rough notes
- Generates a LinkedIn post
- Creates an image prompt
- Generates an image
- Sends both for review

# Tech Stack
- n8n
- OpenAI
- Pollinations AI
- Gmail

# Challenges
- Passing image data between nodes
- Combining outputs
- Prompt tuning
- Making generated posts sound natural

# What I learned
- Prompt engineering matters more than expected.
- Small workflow changes significantly improve the output.
- Breaking tasks into multiple AI steps produces better results than asking a single model to do everything.
