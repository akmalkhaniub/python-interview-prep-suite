# 06: GenAI & Product Context (ImagineArt)

ImagineArt is a top-tier GenAI company. To stand out, you need to understand the intersection of AI and Mobile Engineering.

## 1. AI Integration in Mobile
- **Prompt Engineering UI:** How to build intuitive interfaces for complex AI prompts.
- **Image Generation Pipelines:** Handling long-running generation tasks with Coroutines and showing real-time progress.
- **On-Device vs. Cloud AI:** Understanding when to use local models (like MediaPipe or MLKit) vs. API-based models (DALL-E, Midjourney, or ImagineArt's own model).

## 2. Performance & UX in AI Apps
- **Image Caching:** Displaying high-resolution generated images efficiently (using Coil or specialized KMP image loaders).
- **GPU Acceleration:** Understanding how mobile GPUs handle image rendering.
- **Cost Management:** AI tokens are expensive. How can the app minimize unnecessary API calls?

## 3. The ImagineArt Culture
- **Speed:** Bootstrapped companies move fast. Be prepared to talk about times you shipped a feature in days, not weeks.
- **Ownership:** You aren't just a coder; you are a product owner. Think about how a feature affects the $35M+ ARR.
- **Innovation:** Since they built their own model (ranked #3 globally), they value deep technical understanding and the ability to solve unique problems.

## 4. Potential "Product" Interview Questions
1. **How would you handle a slow image generation process in a mobile app?**
   - *Answer:* Use a robust state management system to show "Generation in Progress". Implement optimistic UI where possible. Use web-sockets or polling for progress updates. Ensure background task management doesn't get killed by the OS.
2. **If a user reports that the "AI generation failed", how would you proactively debug this?**
   - *Answer:* Check Sentry for crashes, check network logs for API failures (4xx/5xx). Implement better error handling and retry logic. Look at the specific prompt/parameters that caused the failure.
3. **How do you balance "Clean Architecture" with the need for "High Speed" in a startup?**
   - *Answer:* Start with a "Pragmatic Clean Architecture". Don't over-engineer layers if they aren't needed yet, but keep the separation of concerns clear so that as the feature grows, it can be easily refactored.
