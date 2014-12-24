---
layout: post
title: HTML5 Playground
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
---
<?php
	$toIgnore = array(".","..","commonAssets",".DS_Store",".git");
	$baseDir = "html5Playground/";
	$items = scandir($baseDir);
	foreach($items as $item) {
		if(!in_array($item,$toIgnore)) {
			$info = false;
			$info = drupal_parse_info_file($baseDir . $item ."/info");
			if ($info) {
				$h5Links[] = array( "data" => l($info["name"],$baseDir . $item,array("attributes"=>array("target"=>"_BLANK"))) . ": " . $info["description"]);
			}
		}
	}
	print theme_item_list(array("items"=>$h5Links,"title"=>null,"type"=>"ul","attributes"=>array()));
?>