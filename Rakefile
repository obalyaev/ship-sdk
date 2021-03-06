require 'rake/packagetask'
require 'rake/clean'
require 'json-schema'
require 'asciidoctor'

VERSION = '1.6'

CLEAN.include('build', 'docs/*.html', 'docs/*.pdf')

date_format = -> data {
  if /\A\d{4}-\d{2}-\d{2}\z/.match(data)
    begin
      Date.parse(data)
    rescue Exception
      raise JSON::Schema::CustomFormatError.new('must be a valid date')
    end
  else
    raise JSON::Schema::CustomFormatError.new('must be a date')
  end
}
JSON::Validator.register_format_validator('date', date_format, ['draft4'])

def validate_schema(schema)
  puts "validate schema #{schema}"
  errors = JSON::Validator.fully_validate_schema(schema)
  raise Exception.new(errors) unless errors.empty?
end

def validate_json(schema, file)
  puts "validate #{file} against #{schema}"
  JSON::Validator.validate!(schema, file)
end

rule '.html' => '.adoc' do |t|
  puts "asciidoctor #{t.source}"
  Asciidoctor.convert_file t.source, in_place: true, safe: :unsafe
end

rule '.pdf' => '.adoc' do |t|
  sh "asciidoctor-pdf #{t.source}"
end

package_task = Rake::PackageTask.new('ship-sdk', VERSION) do |p|
  p.package_dir = 'build'
  p.need_zip = true
  p.package_files.include('schemata/*.json', 'samples/*/*.json')
end

task default: %w[package]

desc 'Generate docs'
task generate_docs: %w[docs/ship.html docs/ship.pdf] do
  package_task.package_files.include('docs/*.html', 'docs/*.pdf', 'docs/*.png')
end

desc 'Validate common definitions'
task :validate_common_definitions do
  schema = 'schemata/common-definitions.json'
  validate_schema(schema)
end

desc 'Validate all guest-stay JSON samples against schema'
task :validate_guest_stays do
  schema = 'schemata/guest-stay-schema.json'
  validate_schema(schema)
  FileList['samples/guest-stays/*.json'].each do |file|
    validate_json(schema, file)
  end
end

desc 'Validate all guest-stay list JSON samples against schema'
task :validate_guest_stay_lists do
  schema = 'schemata/guest-stay-list-schema.json'
  validate_schema(schema)
  FileList['samples/guest-stay-lists/*.json'].each do |file|
    validate_json(schema, file)
  end
end

desc 'Validate all profile JSON samples against schema'
task :validate_profiles do
  schema = 'schemata/profile-schema.json'
  validate_schema(schema)
  FileList['samples/profiles/*.json'].each do |file|
    validate_json(schema, file)
  end
end

desc 'Validate all simple guest-stay JSON samples against schema'
task :validate_simple_guest_stays do
  schema = 'schemata/simple-guest-stay-schema.json'
  validate_schema(schema)
  FileList['samples/simple-guest-stays/*.json'].each do |file|
    validate_json(schema, file)
  end
end

desc 'Validate all simple guest-stay list JSON samples against schema'
task :validate_simple_guest_stay_lists do
  schema = 'schemata/simple-guest-stay-list-schema.json'
  validate_schema(schema)
  FileList['samples/simple-guest-stay-lists/*.json'].each do |file|
    validate_json(schema, file)
  end
end

desc 'Validate all result JSON samples against schema'
task :validate_results do
  schema = 'schemata/result-schema.json'
  validate_schema(schema)
  FileList['samples/results/*.json'].each do |file|
    validate_json(schema, file)
  end
end

desc 'Validate all guest-stay result JSON samples against schema'
task :validate_guest_stay_results do
  schema = 'schemata/guest-stay-result-schema.json'
  validate_schema(schema)
  FileList['samples/guest-stay-results/*.json'].each do |file|
    validate_json(schema, file)
  end
end

desc 'Validate all guest-stay list result JSON samples against schema'
task :validate_guest_stay_list_results do
  schema = 'schemata/guest-stay-list-result-schema.json'
  validate_schema(schema)
  FileList['samples/guest-stay-list-results/*.json'].each do |file|
    validate_json(schema, file)
  end
end

desc 'Validate all profile result JSON samples against schema'
task :validate_profile_results do
  schema = 'schemata/profile-result-schema.json'
  validate_schema(schema)
  FileList['samples/profile-stay-results/*.json'].each do |file|
    validate_json(schema, file)
  end
end

desc 'Validate all profile list result JSON samples against schema'
task :validate_profile_list_results do
  schema = 'schemata/profile-list-result-schema.json'
  validate_schema(schema)
  FileList['samples/profile-list-results/*.json'].each do |file|
    validate_json(schema, file)
  end
end

desc 'Perform tests'
task test: %w[validate_common_definitions validate_guest_stays validate_guest_stay_lists validate_profiles validate_simple_guest_stays validate_simple_guest_stay_lists validate_results validate_guest_stay_results validate_guest_stay_list_results validate_profile_results validate_profile_list_results]

file package_task.package_dir_path => %w[generate_docs test]
