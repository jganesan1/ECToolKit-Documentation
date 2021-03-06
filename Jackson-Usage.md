#Jackson Usgae in EC Toolkit#
Jackson is used in EC Toolkit to serialize data into the database and also to the clients in json format. The Spring controllers act as RESTful web services. Spring automatically detects and uses Jackson for serialization when the jar is available in the classpath. 

##Jackson Configuration##
The configuration of Jackson starts in `JacksonConfiguration` class. The class initializes the application with `JodaModule` to handle time, `MixInModule` to handle the Mix-Ins and `SerializationModule` to handle the messages from the resource bundle.

###JodaModule###
The application use Joda Time to serialize and deserialize the date values. This configuration makes sure that whenever date is serialized to json, it always ISO format in UTC timezone.

###Mix-In###
The application uses Mix-Ins feature of Jackson to present different views of a same given entity/bean. The `JacksonConfiguration` class defines a `MixInModule` class that maps every entity bean to its corresponding MixIn views. The mixin interfaces indicate which property will be serialized or deserialized.

Example:

The `CourseSection` class is mapped to `CourseSectionMixIn` class in the `MixInModule`. Whenever `CourseSection` is serialized properties are available based on the views chosen. As an example this can be seen in the class `PeriodReportingController.groovy`. The various view classes are found in the class `edu.hbs.ectoolkit.converter.json.Views`.

```
	@JsonView(Views.CourseSection.Demand)
	@RequestMapping(value='/courseSectionDemand', method=GET)
	List<CourseSection> courseSectionDemandReport(@PathEntity ElectivePeriod electivePeriod) {
		periodReportingService.buildCourseSectionDemandReport(electivePeriod)
	}

	@JsonView(Views.CourseSection.Verify)
	@RequestMapping(value='/verify', method=GET)
	List<CourseSection> verify(@PathEntity ElectivePeriod electivePeriod) {
		ECPhase.get(electivePeriod?.phaseId)?.sections ?: Collections.emptyList()
	}
```	