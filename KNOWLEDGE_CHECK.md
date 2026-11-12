# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

1. Anyone who can access the public image could extract the credentials and use them to access my AWS resources.
2. The image may be shared publicly, so my credentials could be copied and used by strangers. There is potential for unauthorized charges, data theft, or misuse of my AWS account. 

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

1. The `~/.aws` directory was mounted into the container, giving it access to my AWS credentials.
2. The program used those credentials to authenticate with AWS.
3. `get_atlas_settings()` retrieved the `mongodb_credentials` parameter from AWS Systems Manager Parameter Store.
4. The parameter was decrypted, and the program extracted the MongoDB Atlas URL, username, and password.
5. The program used those settings to connect to MongoDB Atlas, even though I did not pass the three `-e` environment variables.

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

Before: 04911e4f970c
After: bfd2ef020d4d

Reusing the same tag is confusing because the tag points to the newly built image, even though its name remains 0.1.0. Someone might think they are using the original image when they are actually using a different one.
