---
layout: default
permalink: /news/
title: news
description: My Mastodon feed.
nav: false
---

<header class="post-header">
<h1 class="post-title">news</h1>
<p class="post-description">My <a href="https://fediscience.org/@anna">Mastodon</a> feed. Get the RSS file <u><a href="https://fediscience.org/@anna.rss">here</a></u>. </p>
</header>

<hr style="clear:both; color:grey;">

<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<style>
.published-date {
  color: gray;
}
.post-separator {
  border-top: 1px solid #ccc;
  margin-top: 20px;
  padding-top: 20px;
}
.post-content {
  max-width: 600px; /* Adjust this value based on your column width */
  margin: 0 auto; /* To center the content */
}
.post-content img {
  max-width: 100%; /* Ensure images are never wider than the column */
  height: auto; /* Preserve the aspect ratio */
  display: block; /* Remove any extra spacing below the images */
  margin: 10px 0; /* Add some vertical spacing between images and text */
}
</style>

<div id="rss-feed"></div>

<script>
$(document).ready(function() {
  var rssUrl = "https://fediscience.org/@anna.rss";
  var numOfPosts = 10;

  $.ajax(rssUrl, {
    accepts: {
      xml: "application/rss+xml"
    },
    dataType: "xml",
    success: function(data) {
      var feedItems = [];
      $(data)
        .find("item")
        .slice(0, numOfPosts)
        .each(function(index) {
          var item = $(this);
          var title = item.find("title").text();
          var link = item.find("link").text();
          var pubDate = new Date(item.find("pubDate").text());
          var description = item.find("description").text();
          var mediaUrls = []; // Array to store multiple media URLs

          item.find("media\\:content").each(function() {
            var mediaUrl = $(this).attr("url");
            mediaUrls.push(mediaUrl); // Add media URLs to the array
          });

          // Format the date and time in European format with dots
          var options = { day: "numeric", month: "numeric", year: "numeric", hour: "numeric", minute: "numeric" };
          var formattedDate = pubDate.toLocaleString("en-GB", options).replace(/\//g, '.');

          var feedItem = "<div";

          // Conditionally apply the post-separator class
          if (index > 0) {
            feedItem += " class='post-separator'";
          }

          feedItem += ">" +
            "<h3><a href='" + link + "'>" + title + "</a></h3>" +
            "<div class='post-content'>"; // Added post-content div for text and images

          // Add media if available
          if (mediaUrls.length > 0) {
            mediaUrls.forEach(function(mediaUrl) {
              feedItem += "<img src='" + mediaUrl + "' alt='Post Image'>";
            });
          }

          feedItem += "<p>" + description + "</p>" +
            "<p class='published-date'>Published: " + formattedDate + "</p>" +
            "</div>" + // Close post-content div
            "</div>"; // Close post-separator div

          feedItems.push(feedItem);
        });

      $("#rss-feed").html(feedItems.join(""));
    }
  });
});
</script>
