[Previous](./[14]-Generative-AI-And-Large-Language-Models.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[16]-AI-In-The-Real-World.md)

*Modern AI And Applications*

# Lesson 15 - Computer Vision

## 15.1 What Is Computer Vision

**Computer vision** is the field of AI focused on enabling computers to interpret and understand visual information from images and video. This covers a wide range of tasks, from simple ones like determining whether an image contains a particular object, to complex ones like understanding the full scene depicted in a photo, including the objects present, their positions, and how they relate to each other. Computer vision typically relies on the convolutional neural networks introduced in Lesson 12.2, though transformer-based approaches are increasingly used as well.

---

## 15.2 Image Classification And Object Detection

**Image classification** assigns a single label (or a ranked set of likely labels) to an entire image — deciding that a photo is "a photo of a dog," for instance. **Object detection** goes further, identifying not just what objects are present but also where they are located in the image, typically by drawing a bounding box around each detected object and labeling it. Object detection is more computationally demanding than classification since it must consider multiple regions of an image simultaneously, but it's essential for applications like self-driving cars, which need to know not just that a pedestrian is present but exactly where.

---

## 15.3 Image Generation

Just as language models can generate text, **generative image models** can produce entirely new images from scratch, often guided by a text description (a technique called text-to-image generation). Modern image generation commonly uses **diffusion models**, which work by starting from random noise and gradually refining it, step by step, into a coherent image that matches the given prompt. These models have become capable enough to produce photorealistic or highly stylized images, and are increasingly used in design, art, and content creation.

---

## 15.4 Applications Of Computer Vision

Computer vision powers a wide range of real-world applications: facial recognition for unlocking phones or verifying identity, medical imaging analysis to help detect diseases in X-rays or scans, quality control on manufacturing lines to automatically spot defective products, and autonomous vehicles using cameras to perceive their surroundings. Many of these applications carry significant ethical considerations — particularly facial recognition and surveillance use cases — which are discussed further in Lesson 17.

[Previous](./[14]-Generative-AI-And-Large-Language-Models.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[16]-AI-In-The-Real-World.md)
