---
layout: post
title: Pipe Fabrication with QuickPen 3D and CSVs
---
<p><i>I've been told that it's good practice to blog about code you've written, and the thought process behind it.  Doing so gives people an insight into how you approach problems, which at times is more important than what exactly you wrote.  This is such a post.</i></p>

<p>My full-time job is as a mechanical draftsman for an HVAC contractor.  Our work is mostly in high-end residential apartment buildings and hotels in the 5 boroughs of New York City.  We build steam and water-based HVAC systems (sometimes both in the same building).  At work, we use <a href="http://www.quickpen.com/index.php/Products/PipeDesigner-3D-Overview.html">QuickPen Pipe Designer 3D</a>, on top of AutoCAD, to draw 3D models of our mechanical piping.</p>

<center><div><img src="/assets/images/prv.png" class="img-polaroid"/><p><i>A PRV station</i></p></center>

<h3>Coordination</h3>
<p>To get pipe to the field, the draftsmen first take the contract drawings from the architect and engineer, and we draw our pipe on the floor layout.  We then attend coordination meetings, where all trades (plumbers, electricians, sheetmetal, etc.) work on a set of drawings that has everyone's "stuff" on them.  This lets us resolve conflicts and hits.  Pipe moves around, duct gets raised or lowered, and so on.  When all trades sign off on the drawing for a particular floor, the draftsmen are then free to go back to their offices, update their own shop drawings, and work on fabricating their pipe.  The adoption of <a href="http://usa.autodesk.com/building-information-modeling/">BIM</a> and related software stacks promises to make/has made some of these steps unncessary, but many jobs are still coordinated like this.</p>

<h3>Fabrication</h3>
<p>The next goal for the draftsman is to hand the fitters in the shop what are called "fabrication sheets", or "cut sheets", that detail what needs to be fabricated, and what tag that piece should be given.</p>

<center><div><img src="/assets/images/cut-sheets.png" class="img-polaroid"/><p><i>Fabrication pieces</i></p></center>

<p>In the above image, <code>R-03</code> is the tag number given to a piece of pipe that is 1-1/2" in diameter, 9'-6-1/4" long, and has a 1-1/2"x3/4" tee on one end.  We only want one of these pieces tagged R-03.  A fitting on the end of a piece like this is called a "make-up fitting". Usually, threaded pipe like this only has one make-up fitting. The other end of the pipe is a threaded end, and the fitters put these pieces together to construct all the piping on a given floor. We send "shop drawings" to the field that detail how these pieces fit together:</p>

<center><div><img src="/assets/images/balloon-dwg.png" class="img-polaroid"/><p><i>Shop drawing with pipes tagged</i></p></center>

<p>QuickPen makes it (almost) easy to do all this.  I draw my pipe in 3D, at real elevations. I then use QuickPen's ISO manager to create my fabrication pieces by literally clicking on a piece of pipe, and it's make-up fitting.  QuickPen auto-increments the counter as I create pieces.</p>

<h3>The Problem</h3>
<p>While QuickPen makes it easy to tag your pipe for fabrication, and make a shop drawing with ballons for each tag, it doesn't make it THAT easy to create the cut sheets.  When you ask QuickPen to export your Bill Of Materials so that you can make a spreadsheet to print out and give to the fabricators in the shop, you're given the option to export to a CSV.  Here is what QuickPen gives you, truncated for our purposes:</p>

<pre>
"Iso Number","Size","Quantity","Length","Long Description"
"R-02","1-1/2""","1","9'-6 1/4""","PIPE, S/STD A-53 GRADE B ERW T.B.E."
"R-02","1-1/2""x3/4""","1","","TEE REDUCING, THRD, 150LB MALLEABLE IRON"
"S-13","2""","1","9'-6 1/4""","PIPE, S/STD A-53 GRADE B ERW T.B.E."
"S-13","2""x3/4""","1","","TEE REDUCING, THRD, 150LB MALLEABLE IRON"
</pre>

<p>And so on, for each piece.  The CSV is always un-ordered by ISO number (tag number). So basically what you get is a listing of each individual component of a fabrication piece, and not the whole piece itself.  Sometimes this is OK, because there are pieces that may have more than two components.  But 95% of the time a fabrication piece is a length of pipe and a fitting (tee, elbow, valve, coupling, etc.).  I have to massage this CSV so that it's useful to the shop fabricators.</p>

<p>For a while I did this by hand, which was tedious.  I made mistakes as well, which were costly in real dollar terms.  When union steamfitters call the office while they're standing on the 50th floor deck of a building, in the middle of winter, screaming that your pipe doesn't fit...well, that costs everyone money in wasted time and material.  In this way, drafting is different from much of the programming I've done recently.  Aside from the processing of subscriptions, bugs that occur on <a href="https://talentsoup.com">TalentSoup</a> don't <i>immediately</i> result in lost money, but simply an annoyance.  Drafting is different: a mistake could cost tens of thousands of dollars.</p>

<p>So after spending so much time on these CSVs, and making some bad mistakes, I decided to let the computer handle the CSVs for me.  This problem was always begging to be solved by a script.  The result is <a href="http://github.com/cut-sheets">cut-sheets</a>.</p>

<h3>Walk-Through</h3>
<p>Now that I told you more than you wanted to know about mechanical piping, I want to quickly walk though the parsing script.</p>

<p>First, I shamelessly steal Rails' <code>blank?</code> method, which is useful in this context.  Then I set up some constants that map to the column indexes in the CSV.  It's more readable this way. Then I read the CSV in as an array.</p>

<p>
{% highlight ruby %}
require 'csv'

class Object
  def blank?
    respond_to?(:empty?) ? empty? : !self
  end
end

ISO_NUMBER  = 0
SIZE        = 1
QUANTITY    = 2
LENGTH      = 3
DESCRIPTION = 4

fabrication_pieces_as_array = CSV.read("isos.csv")
{% endhighlight %}
</p>

<p>Sometimes, QuickPen's Bill of Materials generator brings in pieces of pipe or fittings that are not part of any fabrication piece.  I remove them from my array so I can process the CSV knowing that I am only working with fabrication pieces. The motivation for want to assume this will be clear shortly.</p>

<p>
{% highlight ruby %}
fabrication_pieces_as_array.delete_if { |x| x[ISO_NUMBER].blank? }
{% endhighlight %}
</p>

<p>Next, I change some of the descriptions of the pieces to conform to our shop standard.  Also, if the current array element is a fitting (which doesn't have a length as it's not a piece of pipe), then I put the fitting size into the description.  This is so when I make this element the make-up fitting of a piece of pipe, I get the fitting size in the description without any other manipulation.</p>

<p>
{% highlight ruby %}
fabrication_pieces_as_array.each do |x|
  x[DESCRIPTION].gsub!(/, 150.*/, '')
  x[DESCRIPTION].gsub!(/ 150.*/, '')
  x[DESCRIPTION].gsub!(' PIPE', '')
  x[DESCRIPTION] = x[SIZE] + " " + x[DESCRIPTION] if x[LENGTH].blank?
end
{% endhighlight %}
</p>

<p>Now that my <code>fabrication_pieces_as_array</code> object is cleaned up, I can start combining pipe pieces with their make-up fittings.  First I create a hash using Ruby's awesome <code>group_by</code> method.  What I get back is a Hash whose keys are the <code>ISO_NUMBER</code>, or tag number.

<p>
<pre>
  {"R-02"=>[["R-02", "1-1/2\"", "1", "9'-6 1/4\"", "PIPE, S/STD A-53 GRADE B ERW T.B.E."],
            ["R-02", "1-1/2\"x3/4\"", "1", "", "1-1/2\"x3/4\" TEE REDUCING, THRD"]],
   "R-03"=>[["R-03", "1-1/2\"", "1", "9'-6 1/4\"", "PIPE, S/STD A-53 GRADE B ERW T.B.E."],
            ["R-03", "1-1/2\"x3/4\"", "1", "", "1-1/2\"x3/4\" TEE REDUCING, THRD"]],
   "R-04"=>[["R-04", "1-1/2\"", "1", "9'-6 1/4\"", "PIPE, S/STD A-53 GRADE B ERW T.B.E."],
            ["R-04", "1-1/2\"x3/4\"", "1", "", "1-1/2\"x3/4\" TEE REDUCING, THRD"]]}
</pre>
</p>

<p>
{% highlight ruby %}
iso_pieces = fabrication_pieces_as_array.group_by{|row| row[ISO_NUMBER]}
{% endhighlight %}
<p>

<p>Now I can test to see how many pieces each tag number has.  As I said before, 95% of the pieces I make will will have two components: a length of pipe and a fitting.  For cases where the pieces are more complex, I handle those manually, usually by making a sketch of the piece in QuickPen.  I'll make a note on the cut sheet to "See Sketch" for that pieces.  Otherwise, I now go through my original array.  For each element I test to see if it's <code>ISO_NUMBER</code> number has two components, and if the <code>LENGTH</code> column is not <code>nil?</code>, meaning this element is the pipe portion of the fabrication piece that also has one make-up fitting.  I add the fitting row to the pipe row because the pipe row has the pipe <code>LENGTH</code>; it just "feels" more natural to do it that way.</p>

<p>
{% highlight ruby %}
fabrication_pieces_as_array.each do |x|
  if iso_pieces[x[ISO_NUMBER]].size == 2 and !x[LENGTH].blank? 
    x[DESCRIPTION] = iso_pieces[x[ISO_NUMBER]].select{ |y| y[LENGTH].blank? }.first[DESCRIPTION]
  end
end
{% endhighlight %}
</p>

<p>Now that I've replaced the <code>DESCRIPTION</code> column of each piece of pipe with the corresponding make-up fitting, I can remove the rows of make-up fittings, which, again, don't have a <code>LENGTH</code> value. Then I sort the array by <code>ISO_NUMBER</code>.  This is not necessary, as I can do this in Excel when add this information to our template, but it saves me a step.</p>

<p>
{% highlight ruby %}
fabrication_pieces_as_array.delete_if {|x| x[LENGTH].blank? and iso_pieces[x[ISO_NUMBER]].size == 2}.sort!{ |x, y| x[ISO_NUMBER] <=> y[ISO_NUMBER] }
{% endhighlight %}
</p>

<p>Lastly, I generate a new CSV with all the pieces and their make-up fittings.  Pieces that have more > 2 components are also listed, but they have not been "combined" together, so I'll resolve them manually. At the bottom of the CSV I also generate a bill of materials, which is a count of each fitting, so the shop knows how many of each to order from the supply house. Ruby's <code>group_by</code> gets all the distinct pieces into a hash, and then it's as simple as getting the <code>size</code> of each key.</p> 

<p>
{% highlight ruby %}
CSV.open("cut-sheets.csv", "wb") do |csv|
  csv << ["ISO", "SIZE", "QUANTITY", "LENGTH", "DESCRIPTION"]
  fabrication_pieces_as_array.each { |x| csv << [x[ISO_NUMBER], x[SIZE], x[QUANTITY], x[LENGTH], x[DESCRIPTION]] }
  fabrication_pieces_as_array.group_by { |x| x[DESCRIPTION] }.each { |key, values| csv << ["", "", values.size, "", key] }
end
{% endhighlight %}
</p>

<p>The finished CSV looks like this:</p>

<p>
<pre>
Iso Number,Size,Quantity,Length,Long Description
R-01,"1-1/4""",1,"9'-6 1/4""","PIPE, S/STD A-53 GRADE B ERW T.B.E."
R-02,"1-1/2""",1,"9'-6 1/4""","1-1/2""x3/4"" TEE REDUCING, THRD"
R-03,"1-1/2""",1,"9'-6 1/4""","1-1/2""x3/4"" TEE REDUCING, THRD"
R-04,"1-1/2""",1,"9'-6 1/4""","1-1/2""x3/4"" TEE REDUCING, THRD"
R-05,"1-1/4""",1,"9'-6 1/4""","1-1/4""x3/4"" TEE REDUCING, THRD"
R-08,"1-1/4""",1,"9'-6 1/4""","1-1/4""x3/4"" TEE REDUCING, THRD"
R-09,"1-1/4""",1,"9'-6 1/4""","1-1/4""x3/4"" TEE REDUCING, THRD"
R-10,"1-1/4""",1,"9'-6 1/4""","1-1/4""x3/4"" TEE REDUCING, THRD"
R-11,"1-1/4""",1,"9'-6 1/4""","1-1/4""x3/4"" TEE REDUCING, THRD"
...
...
"","",3,"","1-1/2""x3/4"" TEE REDUCING, THRD"
"","",8,"","1-1/4""x3/4"" TEE REDUCING, THRD"
"","",9,"","2""x3/4"" TEE REDUCING, THRD"
"","",1,"","2-1/2""x3/4"" TEE REDUCING, THRD"
"","",1,"","2-1/2""x2"" REDUCER, CONC "
"","",1,"","4""x3/4"" THREAD-OLET"
</pre>
</p>

<p>I can plug that into our template, and I am good to go.  Manually, this would take up to an hour depending on how many pieces are on the drawing.  This script takes less than a second.</p>

<p>This is a simple script but it's made my life easier, and I got to write out my thought process, which was fun.  If you have any suggestions for improvements, feel free to reach out.</p>

<h3>See Also</h3>
<p><a href="https://github.com/speric/cut-sheets/">cut-sheets on GitHub</a></p>