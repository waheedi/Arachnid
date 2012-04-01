Arachnid
========
           .....    ..................  ..........................                ..
           ..        ...............  ..  .....................                   ..
                                 ...  ..    ....................                ....
                         .            .I.      ................                 ....
                                      .I...       ...........                  .....
        .                     .    .....=....      .........                 .......
                     ..... ..............:I...    . ........               .........
                   ...I....................=$.      ........            ............
                     ..+ ..................II..      ...               .............
                    ....=..................,+...     ...               .............
                      ...7=.................?...     ....             ..............
                       ....=+...............+...   .....       ............    .....
                       ......?+.............?...    ..     .................... ....
                       .......,??...........$...         ......Z7$OZ,......=,.  ....
                        ........??.........+I..       ......7?7.......+$$Z...  .....
           .               ......ZI7.......$Z...........~7I?=................       
          ..                 .....=7+.....+$=..........=O,...............           
                               ....I$....,IO..........?$.............               
    .                           ...=II...I$I..?77O,..I7+..... .                     
                       . .......  ..IO=..ZO=.I$Z8?$.?7I......                       
                  ...............  .+ZZ..8DZ$7?+O$$~$I,......                   .   
               ........:=7?ZO+8$Z....?Z7.D88=8DZ++IO7,................              
             ....,?+7?=:.......+IO7Z.++$7$8ODD8Z7ODI~................               
             ..=$=. .............=ZIO7~$ZIODN?87OZI...IIZ+87Z,........             .
           ..=O....................,?I$$77ODD8Z+O7?O$II?....,O?I........          ..
         ..,Z. .     .........OO8$OZZO=IO+7$O7+7Z$?+~..........,I7,.....            
      ....O...     ........O=$D=~..7$:~?=?OD7Z???=.....  .........$I....            
      =Z....       .....~7Z,......:=:.IO=+$I7I+OI$77...    .........Z=....          
                  .....I7?........,,....~~7+..+7?+?DO...     .........O....         
                .....+ZI................ ~7:...7+...OZ...     .........I7....       
              .....$77....................:?=..II....IZ...     ...........+$ZZ?.    
             ....+$7............  .............??....?$....      ............  .    
             ...=:........  ...             ...I.....=?.....       ..........       
             ..~?....  .                    ...+......I.....        ...........     
            ..?$..                     .    ..~?......?~.....         .........     
         ....II .                          .. OI..... ZI........       .........    
         ...7..                            ...O,......+I.........       .........   
      . ..=O..                             ...7........$..........       ......     
       .,Z...                              ...Z........7............         .      
      ..,   .                              ...7........8...............             
      ...                                  ...$........O?...............            
       .                                   . =,...  ...~+...............            
                                           ...  .    ...7.................          
                                      .               ..7...................        
                   ...                                 .I.......................    
                                                       ..7.  ...................    
                                                            ...................    .
                                                           ... . .............    ..

Overview
--------

Arachnid was built as an alternative to Anemone, which is a great and powerful ruby spidering library but unfortunately one that succumbs to some pretty serious memory bloat on big sites with a ton of pages. Arachnid relies on Bloom Filters to store the list of visited urls so it's extremely efficient for hundreds of thousands of urls, and the requests are handled by Typhoeus which is much more lightweight than a threaded Mechanize solution. 

Additionally, Arachnid can be threaded with a gem such as Threadify so you can crawl multiple domains with multiple threads each. ...you can thread while you thread.

TL;DR: Give Arachnid a url, it will crawl every single page that it can find on that domain.

###Requirements
Arachnid was built to run on Ruby 1.9.2 I'll be honest, I haven't really tested it on any other platforms, and probably won't in the near future. If you want to make it compatible with 1.8.7, feel free to fork it and go for it. Otherwise, I'd recommend using Anemone instead, as Arachnid was built by a lazy developer :)

###Installation
    gem install arachnid

###Usage

    require 'arachnid'

    Arachnid.new("http://domain.com", {:exclude_urls_with_images => true}).crawl({:threads => 2, :max_urls => 1000}) do |response|
      
        #"response" is just a Typhoeus response object.
        puts response.effective_url

        #You can retrieve the body of the page with response.doc
        parsed_body = Nokogiri::HTML.parse(response.doc)

    end

###Options for Arachnid.new

**:split_url_at_hash => true/false** - For each new url that is discovered on a page, if set to true, Arachnid will split the url at the # in the url and only store the portion before the #. This will allow you to crawl one level deep with # marks (such as a comments page) but not crawl new urls with # in them (such as specific comment permalinks). **:exclude_urls_with_hash** must be set to false for this option to work. Defaults to false.

**:exclude_urls_with_hash => true/false** - Spider will ignore any url with a hash in the url (#). Set to true if crawling blogs or other pages that have a lot of # in permalinks. Defaults to false.

**:exclude_urls_with_images => true/false** - Spider will ignore any url with common image file extensions. Defaults to false.

**:proxy_list => Array** - Spider will choose one proxy at random for each request from the array. Format is: "ip:port:user:pass" or just "ip:port".

###Options for .crawl

**:threads => (num_threads)** - Number of Typhoeus Hydra threads to use when crawling a domain. Out of respect for sites being crawled, keep this number under 10 threads. Defaults to 1.

**:max_urls => (num_urls)** - Total number of pages to crawl on any domain. Use this when crawling large sites or sites with a ton of tag and category pages, as they'll often have tens of thousands of pages with duplicate content and the crawler will run for way too long. Defaults to unlimted urls.
