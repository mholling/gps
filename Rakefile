require 'rexml/document'

task default: "gps.kml"

file "waypoints/huts.kml" => "waypoints/huts.gpx" do |kml|
  xml = REXML::Document.new
  xml << REXML::XMLDecl.new(1.0, "utf-8")
  document = xml.add_element("kml", "xmlns" => "http://www.opengis.net/kml/2.2").add_element("Document")

  REXML::Document.new(File.read "waypoints/huts.gpx").elements.each("gpx/wpt") do |wpt|
    lat, lon, ele = wpt.attributes["lat"], wpt.attributes["lon"], wpt.elements["ele"]&.text
    placemark = document.add_element "Placemark"
    placemark.add_element("name").text = wpt.elements["name"].text
    placemark.add_element("Point").add_element("coordinates").text = [lon, lat, *ele].join(?,)
  end

  string, formatter = String.new, REXML::Formatters::Pretty.new
  formatter.compact = true
  formatter.write xml, string
  File.write kml.to_s, string
end

file "gps.kml" => FileList["rogaines/*.kml", "trips/*.kml", "waypoints/huts.kml"] do |kml|
  xml = REXML::Document.new
  xml << REXML::XMLDecl.new(1.0, "utf-8")
  document = xml.add_element("kml", "xmlns" => "http://www.opengis.net/kml/2.2", "xmlns:gx" => "http://www.google.com/kml/ext/2.2").add_element("Document")

  kml.prerequisites.group_by do |path|
    path.pathmap "%d"
  end.each do |directory, paths|
    folder = document.add_element("Folder")
    folder.add_element("name").text = directory
    paths.each do |path|
      network_link = folder.add_element("NetworkLink")
      network_link.add_element("visibility").text = "0"
      network_link.add_element("name").text = path.pathmap("%n")
      network_link.add_element("Link").add_element("href").text = "https://github.com/mholling/gps/raw/master/#{path}"
      network_link.add_element("flyToView").text = "1"
    end
  end

  string, formatter = String.new, REXML::Formatters::Pretty.new
  formatter.compact = true
  formatter.write xml, string
  File.write kml.to_s, string
end
