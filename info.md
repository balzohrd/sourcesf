gmaps_style = `[{
                    featureType: 'water',
                    elementType: 'all',
                    stylers: [{
                        hue: '#e9ebed'
                    }, {
                        saturation: -78
                    }, {
                        lightness: 67
                    }, {
                        visibility: 'simplified'
                    }]
                }, {
                    featureType: 'landscape',
                    elementType: 'all',
                    stylers: [{
                        hue: '#ffffff'
                    }, {
                        saturation: -100
                    }, {
                        lightness: 100
                    }, {
                        visibility: 'simplified'
                    }]
                }, {
                    featureType: 'road',
                    elementType: 'geometry',
                    stylers: [{
                        hue: '#bbc0c4'
                    }, {
                        saturation: -93
                    }, {
                        lightness: 31
                    }, {
                        visibility: 'simplified'
                    }]
                }, {
                    featureType: 'poi',
                    elementType: 'all',
                    stylers: [{
                        hue: '#ffffff'
                    }, {
                        saturation: -100
                    }, {
                        lightness: 100
                    }, {
                        visibility: 'off'
                    }]
                }, {
                    featureType: 'road.local',
                    elementType: 'geometry',
                    stylers: [{
                        hue: '#e9ebed'
                    }, {
                        saturation: -90
                    }, {
                        lightness: -8
                    }, {
                        visibility: 'simplified'
                    }]
                }, {
                    featureType: 'transit',
                    elementType: 'all',
                    stylers: [{
                        hue: '#e9ebed'
                    }, {
                        saturation: 10
                    }, {
                        lightness: 69
                    }, {
                        visibility: 'on'
                    }]
                }, {
                    featureType: 'administrative.locality',
                    elementType: 'all',
                    stylers: [{
                        hue: '#2c2e33'
                    }, {
                        saturation: 7
                    }, {
                        lightness: 19
                    }, {
                        visibility: 'on'
                    }]
                }, {
                    featureType: 'road',
                    elementType: 'labels',
                    stylers: [{
                        hue: '#bbc0c4'
                    }, {
                        saturation: -93
                    }, {
                        lightness: 31
                    }, {
                        visibility: 'on'
                    }]
                }, {
                    featureType: 'road.arterial',
                    elementType: 'labels',
                    stylers: [{
                        hue: '#bbc0c4'
                    }, {
                        saturation: -93
                    }, {
                        lightness: -2
                    }, {
                        visibility: 'simplified'
                    }]
                }]`


sourceapp =

  # Various string options, most pipe to correspond function or library.
  opts:

    gmaps:
      center: new google.maps.LatLng(37.797509, -122.404070)
      zoom: 17
      scrollwheel: false
      mapTypeId: "roadmap"
      draggable: false
      zoomControl: false
      disableDefaultUI: true
      styles: gmaps_style

    vcarousel:
      interval: 3000

    mq:
      projects: "(min-width: 768px)"
      video: "(min-width: 1150px)"
      team: "(min-width: 1150px)"


  # Modernizr sniffs and hides video if mobile, runs projects() if desktop
  init: ->
    # if mobile, remove video, otherwise on desktop
    if Modernizr.touch
      $('video').remove()
      $('img.fallback').show()
    else
      $('img.fallback').hide()
      @projects()

    @gmaps()
    @scroll()
    @videos()
    @team()




  # Controls video display on desktop
  videos: ->
    # if media query is false, show fallback, else hide it
    if Modernizr.mq(@opts.mq.video) is false
      $('video').hide()
      $('img.fallback').show()
    else
      $('video').show()
      $('img.fallback').hide()

  team: ->
    if Modernizr.mq(@opts.mq.team) is true
      $('.pagination-links').remove()
      if $('#team-carousel').length
        $('#team-carousel').carousel(
          itemsPerPage: 1
          itemsPerTransition: 1
          easing: 'linear'
          speed: 500
        )
      else
        delete $.fn.carousel()
        $('.pagination-links').remove()
        $('.mask').css('height','100%')
        $('.mask ul').css('width','100%')
    else
      delete $.fn.carousel()
      $('.pagination-links').remove()
      $('.mask').css('height','100%')
      $('.mask ul').css('width','100%')

  # this is some hacky code, but basically sniffs the mq and changes the projects area hover states
  projects: ->
    # if on homepage
    if $('.projects').length

      # projects = 3; 100 / projects = 33.33333%
      slice_projects_percentage = 100/$('.projects li').length + "%"

      # Checks break point, if true it sizes desktop, if false it sets for mobile
      if Modernizr.mq(@opts.mq.projects) is true

        # Split the view
        $(".projects li")
          .css("width", slice_projects_percentage)
          .css('height', '100%')

        # hover effect on desktop
        $(".projects li").hover (->
          width = $(this).width();
          parentWidth = $('.projects').offsetParent().width();
          percent = 100*width/parentWidth
          singleli = 50;
          $(this)
            .css("width", singleli + "%")
            .siblings()
            .css("width",  singleli / $(this).siblings().length + "%")
        ), ->
          $(".projects li").css("width", slice_projects_percentage)

      else

        # Removes hover effect
        $('.projects li').unbind("mouseenter mouseleave")

        # Resets the width and changes the hight via percentage
        $(".projects li")
          .css('width','100%')
          .css("height", slice_projects_percentage)



  # Controls background for .navbar
  scroll: ->

    # if 300 pixels from top, apply the dark header, otherwise clear it
    if $(window).scrollTop() >= 300

        $('.navbar')
          .removeClass('background-clear-fade')
          .addClass('background-dark-fade')

    else

      $('.navbar')
        .removeClass('background-dark-fade')
        .addClass('background-clear-fade');

    return


  # Google API
  gmaps: ->
    if $('#mapbg').length
      map = new google.maps.Map(document.getElementById("mapbg"), @opts.gmaps)
      marker = new google.maps.Marker(
        position: @opts.gmaps.center
        title: "monnnnnnn"
      )
      marker.setMap(map)

scrolltoit = (val) ->
  $("html,body").animate
    scrollTop: $("#" + val).offset().top
  , "slow"
  return

# $READY
$(document).ready ->
 # Bootstrap Things
  $('body').scrollspy({ target: '.navbar' })
  $('.carousel').carousel sourceapp.opts.vcarousel
  $("#navbar a").fadeIn("slow").on "click", ->
    scrolltoit $(this).attr("data-id")
    return


  $(".team .col-md-2 img").hover (->
    org = $(this).attr('src')
    hover = $(this).attr('hover')
    $(this)
      .attr('src', $(this).attr('data-hover'))
      .attr('data-org', org)
    return
  ), ->
    $(this)
      .attr('src', $(this).attr('data-org'))
      .attr('data-org', '')
    return

  # starts app
  sourceapp.init()

  # Window resize listener
  $(window).resize ->
    # this seemed to help with auto adjusting
    setTimeout (->
      sourceapp.projects()
      sourceapp.videos()
      sourceapp.team()
    ), 350
  $(".planning").hover(->
    $(this).click()
  ).click ->
    $(this).css("color", "#ccc").siblings().css "color", "#616161"
    $(".sw_construction").hide()
    $(".sw_planning").fadeIn()
    # $(".vstory").src = "newVideo".load().play()


  $(".construction").hover(->
    $(this).click()
  ).click ->
    $(this)
      .css('color', '#ccc')
      .siblings()
      .css('color', '#616161')
    $(".sw_planning").hide()
    $(".sw_construction").fadeIn()
    # $('.vstory')
    #   .src = "newVideo" # URL TO CONSTRUCTION VIDEO
    #   .load()
    #   .play()

  # Listens for .navbar
  document.addEventListener "touchmove", sourceapp.scroll, false # mobile
  document.addEventListener "scroll", sourceapp.scroll, false # Desktop




  # $('#team-carousel ul li').css('width', '30%')
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
-----------------
{
  "__comment": "This is the main config file for the site, almost all string type scenarios are found in here",

  "year"        : "2014",
  "seo"       : {
    "title"     : "lorem",
    "keywords"  : "lorem",
    "description":"lorem",
    "gverify"   : "someverifycode",
    "ganalytics": "ua-213123123",
    "tagline"   : "Source Planning and Construction",
    "__comment" : "This handles the main page SEO"
  },

  "contact"   : {
    "logo"      : "somestring",
    "linkedin"  : "somelink",
    "email"     : "blank",
    "address" : {
      "street"  : "909 Montgomery Street",
      "city"    : "Suite 103 San Francisco",
      "state"   : "CA",
      "zipcode" : "94133",
      "country" : "USA"
    },
    "__comment" : "site wide contact/address"
  },

  "videos"    : {
    "v01"     : {
      "mp4"     : "/assets/small.mp4",
      "fallback": "/assets/400.jpeg"
    },
    "v02"     : {
      "mp4"     : "/assets/small.mp4",
      "fallback": "/assets/400.jpeg"
    },
    "__comment" : "Video locations"
  },
  "___comment": "This controls the projects carousel content",
  "projects"  : [
    {
      "name":"OpenTable",
      "url": "/projects/project1.html",
      "image": "/assets/images/projects/opentable/oplusa-opentable_1346.jpg"
    },
    {
      "name":"OpenTable",
      "url": "/projects/project1.html",
      "image": "/assets/images/projects/opentable/oplusa-opentable_1346.jpg"
    },
    {
      "name":"OpenTable",
      "url": "/projects/project1.html",
      "image": "/assets/images/projects/opentable/oplusa-opentable_1346.jpg"
    },
    {
      "name":"OpenTable",
      "url": "/projects/project1.html",
      "image": "/assets/images/projects/opentable/oplusa-opentable_1346.jpg"
    },
    {
      "name":"Lending Club",
      "url": "/projects/project2.html",
      "image": "/assets/images/projects/lendingclub/Lending_Club_02.jpg"
    },
    {
      "name":"Lending Club",
      "url": "/projects/project2.html",
      "image": "/assets/images/projects/lendingclub/Lending_Club_02.jpg"
    },
    {
      "name":"Invesco",
      "url": "/projects/project3.html",
      "image": "/assets/images/projects/invesco/Invesco_09.jpg"
    }
  ],

  "sliders"   : {
    "__comment" : "Landing slider/vertical slider ",
    "slider_01":[
      {
        "text"  : "lorem",
        "src"   : "/assets/images/projects/invesco/Invesco_02.jpg"
      },
      {
        "text"  : "lorem",
        "src"   : "/assets/images/ot5.jpg"
      },
      {
        "text"  : "lorem",
        "src"   : "/assets/images/projects/invesco/Invesco_02.jpg"
      },
      {
        "text"  : "lorem",
        "src"   : "/assets/images/ot5.jpg"
      },
      {
        "text"  : "lorem",
        "src"   : "/assets/images/ot7.jpg"
      }
    ]
  },

  "__comment" : "Team Members slider",
  "team":[
  {
    "photo": "http://placehold.it/225x325",
    "name" : "somename",
    "title": "some title",
    "slideover":"http://placehold.it/225x325&text=second",
    "bio"  : "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur tincidunt nibh non tortor luctus, sit amet laoreet urna pellentesque. Etiam non lectus ac elit elementum vulputate in non arcu. Phasellus semper rhoncus risus, sed hendrerit libero laoreet ut. Sed cursus nisl sollicitudin sapien euismod feugiat vitae et erat. Mauris ac lorem elit. Duis faucibus adipiscing interdum. Fusce a enim mi. Cras non mauris felis. Duis eu mi justo. Mauris sed vestibulum quam."
  },
  {
    "photo": "http://placehold.it/225x325",
    "name" : "somename",
    "title": "some title",
    "slideover":"http://placehold.it/225x325&text=second",
    "bio"  : "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur tincidunt nibh non tortor luctus, sit amet laoreet urna pellentesque. Etiam non lectus ac elit elementum vulputate in non arcu. Phasellus semper rhoncus risus, sed hendrerit libero laoreet ut. Sed cursus nisl sollicitudin sapien euismod feugiat vitae et erat. Mauris ac lorem elit. Duis faucibus adipiscing interdum. Fusce a enim mi. Cras non mauris felis. Duis eu mi justo. Mauris sed vestibulum quam."
  },
  {
    "photo": "http://placehold.it/225x325",
    "name" : "somename",
    "title": "some title",
    "slideover":"http://placehold.it/225x325&text=second",
    "bio"  : "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur tincidunt nibh non tortor luctus, sit amet laoreet urna pellentesque. Etiam non lectus ac elit elementum vulputate in non arcu. Phasellus semper rhoncus risus, sed hendrerit libero laoreet ut. Sed cursus nisl sollicitudin sapien euismod feugiat vitae et erat. Mauris ac lorem elit. Duis faucibus adipiscing interdum. Fusce a enim mi. Cras non mauris felis. Duis eu mi justo. Mauris sed vestibulum quam."
  },
  {
    "photo": "http://placehold.it/225x325",
    "name" : "somename",
    "title": "some title",
    "slideover":"http://placehold.it/225x325&text=second",
    "bio"  : "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur tincidunt nibh non tortor luctus, sit amet laoreet urna pellentesque. Etiam non lectus ac elit elementum vulputate in non arcu. Phasellus semper rhoncus risus, sed hendrerit libero laoreet ut. Sed cursus nisl sollicitudin sapien euismod feugiat vitae et erat. Mauris ac lorem elit. Duis faucibus adipiscing interdum. Fusce a enim mi. Cras non mauris felis. Duis eu mi justo. Mauris sed vestibulum quam."
  },
  {
    "photo": "http://placehold.it/225x325",
    "name" : "somename",
    "title": "some title",
    "slideover":"http://placehold.it/225x325&text=second",
    "bio"  : "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur tincidunt nibh non tortor luctus, sit amet laoreet urna pellentesque. Etiam non lectus ac elit elementum vulputate in non arcu. Phasellus semper rhoncus risus, sed hendrerit libero laoreet ut. Sed cursus nisl sollicitudin sapien euismod feugiat vitae et erat. Mauris ac lorem elit. Duis faucibus adipiscing interdum. Fusce a enim mi. Cras non mauris felis. Duis eu mi justo. Mauris sed vestibulum quam."
  }
  ],
  "__comment"   : "scripts for site",
  "scripts"    :[
    "//cdnjs.cloudflare.com/ajax/libs/jquery/2.1.1/jquery.min.js",
    "//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js",
    "//cdnjs.cloudflare.com/ajax/libs/modernizr/2.8.1/modernizr.min.js",
    "http://maps.google.com/maps/api/js?sensor=false",
    "/assets/vendor/jquery.carousel.js",
    "/assets/js/app.js"
  ]
}





----------------------
include ../configs/_index

include ../views/_header
.nav-change

.projects_single
  != yield

include ../views/_footer


--------------------------
easeit()
  -moz-transition:width 0.15s ease-out;
  -webkit-transition:width 0.15s ease-out;
  -o-transition:width 0.15s ease-out;
  transition:width 0.15s ease-out;
  -moz-transition-delay:0.15s;
  -webkit-transition-delay:0.15s;
  -o-transition-delay:0.15s;
  transition-delay:0.15s;

.projects
  width 100%
  height 100%
  overflow hidden
  list-style none
  margin 0
  padding 0
  li
    width 100%
    height 33.3%
    position relative

    img
      width 100%
      height:100%
    a
      color white
      text-shadow 1px 1px 3px black
      &:hover
        color white !important

    span
      float left
      position absolute
      bottom 15px
      left 15px
      right 0

// Desktop
@media (min-width: 768px)
  .projects
    li
      float left
      width 10%
      overflow hidden
      height 100%
      easeit()

      a
        span
          float left
          position absolute


        img
          width 100%
          height 100%

        div
        padding 10px

  .projects
    &:hover
      li
        width 10%
    li
      &:hover
        width 50%

