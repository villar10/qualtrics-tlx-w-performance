# qualtrics-tlx

Custom css for NASA-TLX in Qualtrics. This is forked from https://github.com/CMU-TBD/qualtrics-tlx to add the proper scale of "Perfect" to "Failure" as in the original NASA-TLX. It involves slightly more steps as a result of this, but avoids confusion around the scale for that question. 

## Summary

The NASA-TLX (Task Load Index) is the gold standard subjective (self-report) measure of workload. By default, it is administered as a paper-and-pencil questionnaire. Many researchers have administered it digitally using custom-built survey tools, but this doesn’t work well when you need to include the TLX as part of a larger survey. Many papers also reference giving participants the TLX through Qualtrics, but there is a lack of documentation for _how_ these digital versions are designed to mimic (i.e., look and act the same as) a hard copy of the scale. This document explains how to implement the Rating Sheet part of the TLX in Qualtrics.

Description from NASA: https://humansystems.arc.nasa.gov/groups/tlx/index.php

PDF of the paper-and-pencil version: https://humansystems.arc.nasa.gov/groups/tlx/downloads/TLXScale.pdf

## Step 1: Set up a TLX Question in Qualtrics

In your Qualtrics survey, create a “Qualtrics-ified” version of the TLX -- that is, make as close an approximation of it as possible with Qualtrics’ built-in question types.

How to do this:

1. Create a Slider question.
2. Give it 1 statements.
3. Give it a custom start position for the handle in the middle of the scale.
   - Toggle the “Custom start position” feature in the sidebar ON.
   - Toggle “Grid lines” ON. They will make sure your custom start positions stay where you want them to.
   - Give it 3 grid lines, and check the “Snap to grid” box.
   - Drag each handle to the middle of its slider. It should snap to the middle grid line.
   - Toggle “Grid lines” OFF. You don’t want them there for the actual survey. Whatever position you leave each handle in will be its custom start position. Assuming you didn’t move them after positioning them in the middle of the slider, they should stay in the middle.
4. Don’t Add labels or Show value.
5. Add the Question text for the first NASA-TLX item, Mental Demand: How mentally demanding was the task?.


At this point, your complete survey question should look like this:

![Six sliders, one for each item, with tick marks in the middle](/edit-question.png "Question in Survey Builder")

And, if you preview it (three dots at the top right of the question box -> Preview question):

![Four sliders, one for each of the first TLX items, with circular handles in the middle](/preview-question-before-customstyle.png "Question in Preview Question window")

## Step 2: Target your TLX question with JavaScript.

Open your TLX question’s JavaScript window.
With the question selected, scroll down on the left sidebar until you see “</> Javascript”, and open that.
There will probably be some Qualtrics template JS already there. The function you want to edit is the first one, `Qualtrics.SurveyEngine.addOnload()`.
Turn the `onLoad()` function into the following by copy-pasting the code between the brackets into what’s already there:

    Qualtrics.SurveyEngine.addOnload(function()
    {
    	/*Place your JavaScript here to run when the page loads*/
    	var qid = this.questionId;
    	jQuery('#' + qid).attr("id", "nasa-tlx");

    });

This finds the html element that is your question and changes its `id` to `nasa-tlx`. This is necessary because Qualtrics changes question IDs as you add new questions, Auto-number, etc. By always assigning this question the same `id` when it loads, you have a reliable identifier for it.

Then, copy the question you have created within Qualtrics 5 times, such that you have 6 identical "Mental Workload" sliders.
Next, update the questions for each of the 5 additional items in the NASA-TLX, such that your survey contains all 6 questions for the NASA-TLX: 
   - Mental Demand: How mentally demanding was the task?
   - Physical Demand: How physically demanding was the task?
   - Temporal Demand: How hurried or rushed was the pace of the task?
   - Performance: How successful were you in accomplishing what you were asked to do?
   - Effort: How hard did you have to work to accomplish your level of performance?
   - Frustration: How insecure, discouraged, irritated, stressed, and annoyed were you?

At this point, if you preview the survey, you will see that all 6 items are on a scale from "Very Low" to "Very High". This is the what we want for all items except for Performance, which goes from "Perfect" to "Failure". To make this change, we are going to slightly edit the Javascript for just the Performance question. When you open the javascript for the Performance item, it should look like what you inserted in the previous step. We are going to modify this slightly for the Performance question, by changing 'nasa-tlx' in 'jQuery('#' + qid).attr("id", "nasa-tlx");' to 'nasa-tlx-performance' such that the line reads:

jQuery('#' + qid).attr("id", "nasa-tlx-performance");


## Step 3: Apply Custom CSS to turn the sliders into graded scales.

Copy the below css (which can also be found in the [tlx.css](/tlx.css) file in this repo):

#nasa-tlx .track {
    background-image: url(https://github.com/villar10/qualtrics-tlx-w-performance/blob/main/tlx-scale.jpg?raw=true);
    background-size: 770px 80px !important;
    background-repeat: no-repeat;
    height: 80px !important;
}

#nasa-tlx .handle {
    top: 15px !important;
}

#nasa-tlx .statement {
    padding-bottom: 4px;
}

#nasa-tlx .slider-container {
    height: 200px;
}

#nasa-tlx-performance .track {
    background-image: url(https://github.com/villar10/qualtrics-tlx-w-performance/blob/main/tlx-scale_performance.jpg?raw=true);
    background-size: 770px 80px !important;
    background-repeat: no-repeat;
    height: 80px !important;
}

#nasa-tlx-performance .handle {
    top: 15px !important;
}

#nasa-tlx-performance .statement {
    padding-bottom: 4px;
}

#nasa-tlx-performance .slider-container {
    height: 200px;
}
   
Go to the Style tab of your survey’s Look and Feel, and paste it into the Custom CSS box.

Now, when you preview or take the survey, your TLX question should look like this:

![The slider image, but with the graded scales](/qualtrics-tlx-question.png "Tada! NASA-TLX in Qualtrics")

### _Why do I have to copy-paste the code snippets instead of linking to them in this repo?_

It would be great if you could Just Add Tags (*jazz hands*) that link to external resources. But Qualtrics doesn't make this easy! These features seem to exist in the Survey Builder and Look and Feel panels, but they don't work as expected... If we figure it out, we'll update this repo. If you figure it out, please tell us. 🤷‍♀️
