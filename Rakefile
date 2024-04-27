require "open-uri"
require "json"
require "securerandom"

# https://github.com/PurpleBooth/alfred-emoji-snippet-pack/blob/main/bin/generate

file "build" do
  mkdir "build"
end

task :rebuild => "build" do
  rm Dir["build/*.json"]

  URI.open("https://www.unicode.org/emoji/charts/emoji-ordering.txt") do |f|
    f.read.scan(/(?:[^\n])#\s+(\S+)\s+(.*)$/).each do |emoji, name|
      uuid = SecureRandom.uuid

      File.write("build/#{emoji} [#{uuid}].json", JSON.dump({
        alfredsnippet: {
          snippet: emoji,
          dontautoexpand: true,
          uid: uuid,
          name: "#{emoji} #{name}",
          keyword: name.split.map { _1.gsub(":", "") },
        },
      }))
    end
  end
end
task :default => :rebuild

def str_match(a, b)
  return true if a.gsub(/[^\w]+/, "").downcase.include?(b.gsub(/[^\w]+/, "").downcase)
  levenshtein(a, b) < 2
end

def levenshtein(a, b)
  memo = Hash.new {|h,(a,b)|
    h[[a,b]] = (->() {
      return b.length if a.empty?
      return a.length if b.empty?
      return h[[a[1..], b[1..]]] if a[0] == b[0]

      1 + [
        h[[a, b[1..]]],
        h[[a[1..], b]],
        h[[a[1..], b[1..]]],
      ].min
    }).()
  }

  memo[[a, b].map(&:downcase)]
end
