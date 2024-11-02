---
layout: page
permalink: /deprecated/
---
We periodically deprecate some functionality to evolve the product to a better place. This page will explain what's changed and what you'll need to change in your config to make it work. In the simple case where something has just been renamed then the error message will tell you what to do. In other cases the explanation will be here.

***

# project, filter, and jql are no longer supported in the download section

This is a side effect of the fact that we need to know the board for every issue. If you really need this functionality then create a custom board in Jira that uses the filter you care about and specify that board in your config.

# Boards are now a top level declaration

It used to be that you would declare a board in the download section and that you would declare a cycle time directly in the html_report section and the two would be linked only by the board id.

```ruby
download do
  board_ids 2, 3
end

file do
  file_suffix '.html'
  html_report do
    board_id 2
    cycletime do
      start_at first_time_in_status_category('In Progress')
      stop_at currently_in_status_category('Done')
    end
  end
end
```

Now we declare the board as a top level directive and attach the cycle time directly to it. This should be at the top of the project section as shown.

```ruby
project do
  download do
    # ...
  end

  board id: 2 do
    cycletime do
      start_at first_time_in_status_category('In Progress')
      stop_at currently_in_status_category('Done')
    end
  end

  file do
    # ...
  end
end
```

Why is this being restructured? Other than generally being cleaner, this will allow us to do things like aggregating data across multiple projects for portfolio level reports. Something that we couldn't do before.

# Expedited priority names should now be specified in the board rather than in the chart

It used to be that a couple of charts took a priority name as a parameter. These are all deprecated and we should specify expedited priority names in the board instead. This will allow reports that aggregate boards to work in a more reasonable way. Also, we can specify multiple priority names now.

```ruby
  board id: 2 do
    cycletime do
      start_at first_time_in_status_category('In Progress')
      stop_at currently_in_status_category('Done')
    end
    expedited_priority_names 'Critical', 'Highest'
  end
```