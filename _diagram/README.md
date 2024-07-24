# Convert to Gif

based on https://gist.github.com/SheldonWangRJT/8d3f44a35c8d1386a396b9b49b43c385

ffmpeg -i /Users/rhyando/Desktop/Screen\ Recording\ 2024-06-23\ at\ 08.51.57.mov -pix_fmt rgb24 -r 10 output.gif && gifsicle -O3 output.gif -o output.gif

or use this https://new.express.adobe.com/tools/convert-to-gif