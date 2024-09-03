const http = require("http");
const https = require("https");


const timeUrl = "https://time.com";

const server = http.createServer((req, res) => {
  if (req.method === "GET" && req.url === "/getLatestStories") {
    https
      .get(timeUrl, (response) => {
        let data = "";

        response.on("data", (chunk) => {
          data += chunk;
        });

        response.on("end", () => {
          if (response.statusCode === 200) {
            const latestStories = [];

            // Find the latest stories section based on the HTML structure
            const startMarker = '<div class="partial latest-stories"';
            const endMarker = "</ul>";
            const startIndex = data.indexOf(startMarker);
            const endIndex = data.indexOf(endMarker, startIndex);

            if (startIndex !== -1 && endIndex !== -1) {
              const latestStoriesHtml = data.substring(startIndex, endIndex);

              // Use regular expressions to extract titles and links
              const titleRegex =
                /<h3 class="latest-stories__item-headline">([^<]+)<\/h3>/g;
              const linkRegex = /<a href="([^"]+)">/g;

              let match;
              while ((match = titleRegex.exec(latestStoriesHtml)) !== null) {
                const title = match[1].trim();
                const linkMatch = linkRegex.exec(latestStoriesHtml);
                if (linkMatch) {
                  const link = `${timeUrl}${linkMatch[1]}`;
                  latestStories.push({ title, link });
                }
              }

              // Send the JSON response with the top 6 latest stories
              res.writeHead(200, { "Content-Type": "application/json" });
              res.end(JSON.stringify(latestStories.slice(0, 6)));
            } else {
              res.writeHead(500, { "Content-Type": "application/json" });
              res.end(
                JSON.stringify({
                  error: "Latest stories section not found on the page",
                })
              );
            }
          } else {
            res.writeHead(response.statusCode, {
              "Content-Type": "application/json",
            });
            res.end(
              JSON.stringify({ error: "Failed to fetch data from Time.com" })
            );
          }
        });
      })
      .on("error", (error) => {
        console.error("Error:", error);
        res.writeHead(500, { "Content-Type": "application/json" });
        res.end(JSON.stringify({ error: "Internal server error" }));
      });
  } else {
    // Handle other routes or methods here
    res.writeHead(404, { "Content-Type": "text/plain" });
    res.end("Not Found");
  }
});

const port = process.env.PORT || 3000;

server.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});
